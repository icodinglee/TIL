
**1.减少Store中的更新次数。将更少的数据放在store中。**
![减少store的更新次数](http://upload-images.jianshu.io/upload_images/5545478-5c7cb8ac047747ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

充分利用React组件化的能力，使得不必要的数据不再放入Store中。一般组件涉及到的数据分为两类：一种是组件内部使用的数据，不与其他组件发生耦合或影响的数据；另外一种就是业务数据，影响多组件的数据或者多组件共享数据。

将组件内部使用的数据迁移到组件内， 拆分开之后将大大减少store的更新次数。

**2.减少不必要的组件更新，也就是说当数据被更新之后，如果相应的组件内的数据没有被更新，那么这个组件就不要进行重复的计算。**
![减少不必要的组件更新](http://upload-images.jianshu.io/upload_images/5545478-c05bfe4c1ddc25de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

shouldComponentUpdate: 当新数据进来之后,React组件会调用一个函数。ShouldComponentUpdate来判断是否进行下一步的渲染，所以我们可以在这个函数中做一些基本的判断，来决定是否进行下一步的计算。这里有两个判断方法，一种采用深度比较，一种采用浅度比较。
1. 深度比较可以很好的判断数据是否发生改变，**但 deepCopy 和 deepCompare 一般都是非常耗性能的**。

2.另一种是浅度比较，这种比较方法只需要比较数据的引用是否发生变化即可，这种比较方法效率高但是对数据的更新和类型有一定的要求。

***这种数据结构需要满足一旦数据被更新，那么它的引用就会被更新；而数据没更新，那么它的引用也不会发生变化。 这里我们有两个选择 ：immutable.js 和 react-addon-update***

- immutable.js 
  ImmutableJs是一套fackbook提供的数据结构，这套数据结构无法被直接更改数据一旦被更改，它的引用也会相应的发生变化，其内部使用的了非常高效的算法能够复用很多数据，所以对immutableJs的数据更改非常高效。
**Immutable 则提供了简洁高效的判断数据是否变化的方法，只需 === 和 is 比较就能知道是否需要执行 render()，而这个操作几乎 0 成本，所以可以极大提高性能。**

相对而言的限制： 
  -  immutableJs是一套新的数据结构，所以操作的时候需要查询它的api来完成，但用它的API很多，很杂，而JS语言本身没有静态类型检查，在使用时候非常容易出错，所以这里建议可能需要配合Typescript等支持语法检查的语言一起使用，这样能够编译阶段检查出来问题；
  -  使用ImmutableJs生成的数据结构，无法使用现有的各种js库进行操作，这会造成很大的不方便。 

- react-addon-update
这个插件接收两个参数，一个是更新的对象，另一个我们叫做spec,代指你告诉这个函数该如何更新对象。在更新数据的时候可以避免克隆操作，同时又能够达到ImmutableJS数据结构的效果。

![使用react-addons-update提高数据层计算效率](http://upload-images.jianshu.io/upload_images/5545478-a256e7284143f335.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

(在深度克隆方面，Object.assign 效率不低，属于shallowClone，但是如果对象数据有多层的话，就需要手动去做这件事情，才能保证上面提到的引用数据效果。使用 react-addon-update 或者 ImmutableJs 的话，代码看起来会比较简洁。)

**总结来看，在使用redux + react的过程中，我们想要提供他的性能的话，可以从这三个方面入手：**

- 减少store的更新次数，这个主要通过组件化来解决，ui 和临时数据不再存放在Store中。

- 避免不必要的Component渲染，假设数据层能够提供准确的数据更新，即数据更新了，数据引用也会发生改变，数据没有更新，那么它的引用就不会发生变化，这样的话我们可以在ShouldComponentUpdate函数进行判断是否进行下一步的计算。

- 提高数据层的计算效率，避免克隆操作, 通过使用immutablejs 或者react-addon-update来达到只更新想要更新的数据。
