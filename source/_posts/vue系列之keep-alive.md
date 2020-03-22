---
title: vue系列之keep-alive
date: 2019-08-20 16:01:08
tags: vue
---
### keep-alive是什么
keep-alive是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在父组件链中；使用keep-alive包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。

<!-- more -->

### 场景
填写过信息的页面，筛选过条件的页面，后退时保留前一页数据继续操作等等

### keep-alive用法
- 在动态组件中的应用


    <keep-alive :include="whiteList" :exclude="blackList" :max="amount">
      <component :is="currentComponent"></component>
    </keep-alive>

- 在vue-router中的应用


    <keep-alive :include="whiteList" :exclude="blackList" :max="amount">
      <router-view></router-view>
    </keep-alive>

    
include定义缓存白名单，keep-alive会缓存命中的组件；exclude定义缓存黑名单，被命中的组件将不会被缓存；max定义缓存组件上限，超出上限使用LRU的策略置换缓存数据。

### keep-alive源码

keep-alive.js内部另外还定义了一些工具函数，我们按住不表，先看它对外暴露的对象。

    export default {
      name: 'keep-alive',
      abstract: true, // 判断当前组件虚拟dom是否渲染成真是dom的关键
    
      props: {
        include: patternTypes, // 缓存白名单
        exclude: patternTypes, // 缓存黑名单
        max: [String, Number] // 缓存的组件实例数量上限
      },
    
      created () {
        this.cache = Object.create(null) // 缓存虚拟dom
        this.keys = [] // 缓存的虚拟dom的健集合
      },
    
      destroyed () {
        for (const key in this.cache) { // 删除所有的缓存
          pruneCacheEntry(this.cache, key, this.keys)
        }
      },
    
      mounted () {
        // 实时监听黑白名单的变动
        this.$watch('include', val => {
          pruneCache(this, name => matches(val, name))
        })
        this.$watch('exclude', val => {
          pruneCache(this, name => !matches(val, name))
        })
      },
    
      render () {
        // 先省略...
      }
    }
可以看出，与我们定义组件的过程一样，先是设置组件名为keep-alive，其次定义了一个abstract属性，值为true。props属性定义了keep-alive组件支持的全部参数。
keep-alive在它生命周期内定义了三个钩子函数：
- created
初始化两个对象分别缓存VNode（虚拟DOM）和VNode对应的键集合
- destroyed
删除this.cache中缓存的VNode实例。这里不是简单地将this.cache置为null，而是遍历调用pruneCacheEntry函数删除。


    function pruneCacheEntry (
      cache: VNodeCache,
      key: string,
      keys: Array<string>,
      current?: VNode
    ) {
      const cached = cache[key]
      if (cached && (!current || cached.tag !== current.tag)) {
        cached.componentInstance.$destroy() // 执行组件的destory钩子函数,删除缓存VNode还要对应执行组件实例的destory钩子函数。
      }
      cache[key] = null
      remove(keys, key)
    }
- mounted
在mounted这个钩子中对include和exclude参数进行监听，然后实时地更新（删除）this.cache对象数据。pruneCache函数的核心也是去调用pruneCacheEntry。
- render
第一步：获取keep-alive包裹着的第一个子组件对象及其组件名；
第二步：根据设定的黑白名单（如果有）进行条件匹配，决定是否缓存。不匹配，直接返回组件实例（VNode），否则执行第三步；
第三步：根据组件ID和tag生成缓存Key，并在缓存对象中查找是否已缓存过该组件实例。如果存在，直接取出缓存值并更新该key在this.keys中的位置（更新key的位置是实现LRU置换策略的关键），否则执行第四步；
第四步：在this.cache对象中存储该组件实例并保存key值，之后检查缓存的实例数量是否超过max的设置值，超过则根据LRU置换策略删除最近最久未使用的实例（即是下标为0的那个key）。
第五步：最后并且很重要，将该组件实例的keepAlive属性值设置为true。


      render () {
        const slot = this.$slots.default
        const vnode: VNode = getFirstComponentChild(slot) // 找到第一个子组件对象
        const componentOptions: ?VNodeComponentOptions = vnode && vnode.componentOptions
        if (componentOptions) { // 存在组件参数
          // check pattern
          const name: ?string = getComponentName(componentOptions) // 组件名
          const { include, exclude } = this
          if ( // 条件匹配
            // not included
            (include && (!name || !matches(include, name))) ||
            // excluded
            (exclude && name && matches(exclude, name))
          ) {
            return vnode
          }
    
          const { cache, keys } = this
          const key: ?string = vnode.key == null // 定义组件的缓存key
            // same constructor may get registered as different local components
            // so cid alone is not enough (#3269)
            ? componentOptions.Ctor.cid + (componentOptions.tag ? `::${componentOptions.tag}` : '')
            : vnode.key
          if (cache[key]) { // 已经缓存过该组件
            vnode.componentInstance = cache[key].componentInstance
            // make current key freshest
            remove(keys, key)
            keys.push(key) // 调整key排序
          } else {
            cache[key] = vnode // 缓存组件对象
            keys.push(key)
            // prune oldest entry
            if (this.max && keys.length > parseInt(this.max)) { // 超过缓存数限制，将第一个删除
              pruneCacheEntry(cache, keys[0], keys, this._vnode)
            }
          }
    
          vnode.data.keepAlive = true // 渲染和执行被包裹组件的钩子函数需要用到
        }
        return vnode || (slot && slot[0])
      }
      
      
### keep-alive组件的渲染
我们用过keep-alive都知道，它不会生成真正的DOM节点，这是怎么做到的？

    // src/core/instance/lifecycle.js
    export function initLifecycle (vm: Component) {
      const options = vm.$options
      // 找到第一个非abstract的父组件实例
      let parent = options.parent
      if (parent && !options.abstract) {
        while (parent.$options.abstract && parent.$parent) {
          parent = parent.$parent
        }
        parent.$children.push(vm)
      }
      vm.$parent = parent
      // ...
    }
Vue在初始化生命周期的时候，为组件实例建立父子关系会根据abstract属性决定是否忽略某个组件。在keep-alive中，设置了abstract: true，那Vue就会跳过该组件实例。最后构建的组件树中就不会包含keep-alive组件，那么由组件树渲染成的DOM树自然也不会有keep-alive相关的节点了

### keep-alive包裹的组件是如何使用缓存的？

在patch阶段，会执行createComponent函数：

    // src/core/vdom/patch.js
    function createComponent (vnode, insertedVnodeQueue, parentElm, refElm) {
        let i = vnode.data
        if (isDef(i)) {
          const isReactivated = isDef(vnode.componentInstance) && i.keepAlive
          if (isDef(i = i.hook) && isDef(i = i.init)) {
            i(vnode, false /* hydrating */)
          }
    
          if (isDef(vnode.componentInstance)) {
            initComponent(vnode, insertedVnodeQueue)
            insert(parentElm, vnode.elm, refElm) // 将缓存的DOM（vnode.elm）插入父元素中
            if (isTrue(isReactivated)) {
              reactivateComponent(vnode, insertedVnodeQueue, parentElm, refElm)
            }
            return true
          }
        }
      }

在首次加载被包裹组件时，由keep-alive.js中的render函数可知，vnode.componentInstance的值是undefined，keepAlive的值是true，因为keep-alive组件作为父组件，它的render函数会先于被包裹组件执行；那么就只执行到i(vnode, false /* hydrating */)，后面的逻辑不再执行；
再次访问被包裹组件时，vnode.componentInstance的值就是已经缓存的组件实例，那么会执行insert(parentElm, vnode.elm, refElm)逻辑，这样就直接把上一次的DOM插入到了父元素中。

### 只执行一次的钩子

一般的组件，每一次加载都会有完整的生命周期，即生命周期里面对应的钩子函数都会被触发，为什么被keep-alive包裹的组件却不是呢？ 被缓存的组件实例会为其设置keepAlive = true，而在初始化组件钩子函数中：

    // src/core/vdom/create-component.js
    const componentVNodeHooks = {
      init (vnode: VNodeWithData, hydrating: boolean): ?boolean {
        if (
          vnode.componentInstance &&
          !vnode.componentInstance._isDestroyed &&
          vnode.data.keepAlive
        ) {
          // kept-alive components, treat as a patch
          const mountedNode: any = vnode // work around flow
          componentVNodeHooks.prepatch(mountedNode, mountedNode)
        } else {
          const child = vnode.componentInstance = createComponentInstanceForVnode(
            vnode,
            activeInstance
          )
          child.$mount(hydrating ? vnode.elm : undefined, hydrating)
        }
      }
      // ...
    }

可以看出，当vnode.componentInstance和keepAlive同时为truly值时，不再进入$mount过程，那mounted之前的所有钩子函数（beforeCreate、created、mounted）都不再执行。

### 酌情决定是否使用已缓存的缓存

虽然官方提供了include和exclude特性，说你可以决定哪些页面使用缓存哪些页面不用缓存，但没有解决酌情决定是否使用已缓存的缓存，所以想到了一个方法在离开页面时销毁这个页面是不是就可以了，然而并不能，组件销毁了缓存还在，核心原因就在于 keep-alive 不能正确处理已销毁的组件。

打印vue组件变量时,发现下面有$vnode->parent->tag是keep-alive的组件，点开以后发现componentInstance下有个cache。在销毁组件之前，寻找路由组件所在父级的 keep-alive 组件，操控其中的 cache 列表，强行删除其中的缓存

    //使用Vue.mixin的方法拦截了路由离开事件，并在该拦截方法中实现了销毁页面缓存的功能。
    Vue.mixin({
        beforeRouteLeave:function(to, from, next){
            if (from && from.meta.rank && to.meta.rank && from.meta.rank>to.meta.rank)
            {//此处判断是如果返回上一层，你可以根据自己的业务更改此处的判断逻辑，酌情决定是否摧毁本层缓存。
                if (this.$vnode && this.$vnode.data.keepAlive)
                {
                    if (this.$vnode.parent && this.$vnode.parent.componentInstance && this.$vnode.parent.componentInstance.cache)
                    {
                        if (this.$vnode.componentOptions)
                        {
                            var key = this.$vnode.key == null
                                        ? this.$vnode.componentOptions.Ctor.cid + (this.$vnode.componentOptions.tag ? `::${this.$vnode.componentOptions.tag}` : '')
                                        : this.$vnode.key;
                            var cache = this.$vnode.parent.componentInstance.cache;
                            var keys  = this.$vnode.parent.componentInstance.keys;
                            if (cache[key])
                            {
                                if (keys.length) {
                                    var index = keys.indexOf(key);
                                    if (index > -1) {
                                        keys.splice(index, 1);
                                    }
                                }
                                delete cache[key];
                            }
                        }
                    }
                }
                this.$destroy();
            }
            next();
        },
    });

### 设计缓存

最常见的一个场景是结算页选择地址，结算页是一个路由，选择地址又是一个路由页面，我们希望用户选择完地址回到结算页时，结算页里的其他数据比如选择的优惠券啊都能继续保持。
但特别是一些表单场景，我们既希望填写一半进入下一页面时能保留填写的数据，我们又希望新进入的表单是一个全新的表单页。
换句话说，回到上一个页面时使用缓存，进入下一个页面时不使用缓存，
再换句话说，所有页面都用缓存，只在后退（回到上一页）时移除当前页缓存，这样下一次前进（进入当前页）时因为没有缓存就自然使用全新页面，
也就是说，只要实现后退（回到上一页）时移除当前页缓存这个功能，就可以了。



