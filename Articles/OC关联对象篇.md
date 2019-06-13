## runtime关联对象的基本使用

runtime关联对象有什么用途呢？我们可以利用关联对象来给Category(分类)间接地添加成员变量而且关联对象不会影响到原来类对象的内存结构。

objc/runtime.h中提供的关联对象的API有以下3个：

* (1)添加关联对象

&emsp;&emsp; void objc_setAssociatedObject(id object, const void * key,id value, objc_AssociationPolicy policy)

* (2)获取关联对象

&emsp;&emsp;id objc_getAssociatedObject(id object, const void * key)

* (3)移除所有的关联对象

&emsp;&emsp; void objc_removeAssociatedObjects(id object)


其中的参数key的常见用法有以下几种：

```
//用法1：
static void *MyKey = &MyKey;
objc_setAssociatedObject(obj, MyKey, value, OBJC_ASSOCIATION_RETAIN_NONATOMIC)
objc_getAssociatedObject(obj, MyKey)

```

```
//用法2：
static char MyKey;
objc_setAssociatedObject(obj, &MyKey, value, OBJC_ASSOCIATION_RETAIN_NONATOMIC)
objc_getAssociatedObject(obj, &MyKey)

```

```
//用法3：使用属性名作为key
objc_setAssociatedObject(obj, @"property", value, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
objc_getAssociatedObject(obj, @"property");

```

```
//用法4：使用get方法的@selecor作为key，此方法可读性更高，推荐使用方法4
objc_setAssociatedObject(obj, @selector(getter), value, OBJC_ASSOCIATION_RETAIN_NONATOMIC)
objc_getAssociatedObject(obj, @selector(getter))

```

其中的参数objc_AssociationPolicy(关联策略)的枚举值有以下几个：

* OBJC_ASSOCIATION_ASSIGN  其对应的修饰符是assign
* OBJC_ASSOCIATION_RETAIN_NONATOMIC 其对应的修饰符是strong,nonatomic
* OBJC_ASSOCIATION_COPY_NONATOMIC 对应的修饰符是copy,nonatomic
* OBJC_ASSOCIATION_RETAIN 对应的修饰符是strong,atomic
* OBJC_ASSOCIATION_COPY 其对应的修饰符是copy,atomic

## 添加关联对象objc_setAssociatedObject的底层实现原理


Apple源码地址：<https://opensource.apple.com/tarballs/objc4/>

objc4-750的objc-references.mm中objc_setAssociatedObject方法源码如下：

(1) objc_setAssociatedObject的底层实现：objc_setAssociatedObject中直接调用了_object_set_associative_reference(object, (void *)key, value, policy)

```
void objc_setAssociatedObject(id object, const void *key, id value, objc_AssociationPolicy policy) {
    _object_set_associative_reference(object, (void *)key, value, policy);
}
```

(2) _object_set_associative_reference的底层实现

```
void _object_set_associative_reference(id object, void *key, id value, uintptr_t policy) {
    // retain the new value (if any) outside the lock.
    ObjcAssociation old_association(0, nil);
    id new_value = value ? acquireValue(value, policy) : nil;
    {
        AssociationsManager manager;
        AssociationsHashMap &associations(manager.associations());
        disguised_ptr_t disguised_object = DISGUISE(object);
        //new_value表示设置关联对象objc_setAssociatedObject中的value值是否存在
        if (new_value) {
            // break any existing association.
            ////通过disguised_object找到i,i->second得到ObjectAssociationMap
            AssociationsHashMap::iterator i = associations.find(disguised_object);
            if (i != associations.end()) {
                // secondary table exists
                ObjectAssociationMap *refs = i->second;
                //通过key找到j,j->second得到ObjcAssociation（old_association），设置值。如果没有找到j，设置新关联对象。
                ObjectAssociationMap::iterator j = refs->find(key);
                if (j != refs->end()) {
                    old_association = j->second;
                    j->second = ObjcAssociation(policy, new_value);
                } else {
                    (*refs)[key] = ObjcAssociation(policy, new_value);
                }
            } else {
                // create the new association (first time).
                ////如果上面通过i没有找到ObjectAssociationMap，说明这个对象没有设置过关联对象，就创建一个ObjectAssociationMap对象，再关联对象
                ObjectAssociationMap *refs = new ObjectAssociationMap;
                associations[disguised_object] = refs;
                (*refs)[key] = ObjcAssociation(policy, new_value);
                object->setHasAssociatedObjects();
            }
        } else {
            // setting the association to nil breaks the association.
            //如果参数value是nil，则关联对象被移除（refs->erase(j);）
            AssociationsHashMap::iterator i = associations.find(disguised_object);
            if (i !=  associations.end()) {
                ObjectAssociationMap *refs = i->second;
                ObjectAssociationMap::iterator j = refs->find(key);
                if (j != refs->end()) {
                    old_association = j->second;
                    refs->erase(j);
                }
            }
        }
    }
    // release the old value (outside of the lock).
    if (old_association.hasValue()) ReleaseValue()(old_association);
}

```


(3)其中的AssociationsManager的内部实现

```
class AssociationsManager {
    // associative references: object pointer -> PtrPtrHashMap.
    static AssociationsHashMap *_map;
public:
    AssociationsManager()   { AssociationsManagerLock.lock(); }
    ~AssociationsManager()  { AssociationsManagerLock.unlock(); }
    
    AssociationsHashMap &associations() {
        if (_map == NULL)
            _map = new AssociationsHashMap();
        return *_map;
    }
};
```

(4) 再看AssociationsHashMap的底层实现

```
class AssociationsHashMap : public unordered_map<disguised_ptr_t, ObjectAssociationMap *, DisguisedPointerHash, DisguisedPointerEqual, AssociationsHashMapAllocator> {
public:
    void *operator new(size_t n) { return ::malloc(n); }
    void operator delete(void *ptr) { ::free(ptr); }
};
```
    
(5) ObjectAssociationMap的底层实现

```
class ObjectAssociationMap : public std::map<void *, ObjcAssociation, ObjectPointerLess, ObjectAssociationMapAllocator> {
public:
    void *operator new(size_t n) { return ::malloc(n); }
    void operator delete(void *ptr) { ::free(ptr); }
};
```
    
(6) ObjcAssociation的内部实现

```
class ObjcAssociation {
    uintptr_t _policy;
    id _value;
public:
    ObjcAssociation(uintptr_t policy, id value) :_policy(policy), _value(value) {}
    ObjcAssociation() : _policy(0), _value(nil) {}

    uintptr_t policy() const { return _policy; }
    id value() const { return _value; }
    
    bool hasValue() { return _value != nil; }
};  
```


实现关联对象技术的核心对象有：

* AssociationsManager
* AssociationHashMap
* ObjectAssociationMap
* ObjcAssociation


以上源码可以简化为以下逻辑关系图：

![关联对象原理示意图.png](https://upload-images.jianshu.io/upload_images/4164292-6067b1e1540b88ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

关联对象并不是存储在被关联对象本身内存中，而是存储在全局的统一的AssociationsManager中。如果设置某个关联对象的value为nil，相当于移除该关联对象。



## 获取关联对象objc_getAssociatedObject的底层实现原理


objc4-750的objc-references.mm中objc_getAssociatedObject方法源码如下：

（1）objc_getAssociatedObject的内部实现

```
id objc_getAssociatedObject(id object, const void *key) {
    return _object_get_associative_reference(object, (void *)key);
}
```

（2）_object_get_associative_reference的底层实现

```
id _object_get_associative_reference(id object, void *key) {
    id value = nil;
    uintptr_t policy = OBJC_ASSOCIATION_ASSIGN;
    {
        AssociationsManager manager;
        AssociationsHashMap &associations(manager.associations());
        disguised_ptr_t disguised_object = DISGUISE(object);
        AssociationsHashMap::iterator i = associations.find(disguised_object);
        if (i != associations.end()) {
            ObjectAssociationMap *refs = i->second;
            ObjectAssociationMap::iterator j = refs->find(key);
            if (j != refs->end()) {
                ObjcAssociation &entry = j->second;
                value = entry.value();
                policy = entry.policy();
                if (policy & OBJC_ASSOCIATION_GETTER_RETAIN) {
                    objc_retain(value);
                }
            }
        }
    }
    if (value && (policy & OBJC_ASSOCIATION_GETTER_AUTORELEASE)) {
        objc_autorelease(value);
    }
    return value;
}
```

首先通过传进来的参数object计算得到disguised_object，再将disguised_object作为associations.find方法的参数传入并得到i，再通过i->second，得到ObjectAssociationMap *refs对象；然后refs通过参数key计算得到j，利用j->second，得到ObjcAssociation &entry；又通过entry.value()方法和entry.policy()方法分别计算得到value和policy（关联策略），最后返回value值。


## 移除所有关联对象objc_removeAssociatedObjects的内部实现

objc4-750的objc-references.mm中objc_removeAssociatedObjects方法源码如下：

```
void objc_removeAssociatedObjects(id object) 
{
    if (object && object->hasAssociatedObjects()) {
        _object_remove_assocations(object);
    }
}
```

_object_remove_assocations(object)的内部实现如下：

```
void _object_remove_assocations(id object) {
    vector< ObjcAssociation,ObjcAllocator<ObjcAssociation> > elements;
    {
        AssociationsManager manager;
        AssociationsHashMap &associations(manager.associations());
        if (associations.size() == 0) return;
        disguised_ptr_t disguised_object = DISGUISE(object);
        AssociationsHashMap::iterator i = associations.find(disguised_object);
        if (i != associations.end()) {
            // copy all of the associations that need to be removed.
            ObjectAssociationMap *refs = i->second;
            for (ObjectAssociationMap::iterator j = refs->begin(), end = refs->end(); j != end; ++j) {
                elements.push_back(j->second);
            }
            // remove the secondary table.
            delete refs;
            associations.erase(i);
        }
    }
    // the calls to releaseValue() happen outside of the lock.
    for_each(elements.begin(), elements.end(), ReleaseValue());
}
```
首先调用_object_remove_assocations函数，在_object_remove_assocations内部先通过参数object计算得到disguised_object，再通过disguised_object计算获得i，然后通过i->second获得ObjectAssociationMap *refs，接下来遍历ref获得j,移除j->second（关联对象）也就是把关联对象的hashMap清空，最后将对象的这个hasMap从全局的AssociationsHashMap中移除。

