# GamePlay-GameFreamWork

## UEGameFreamWork

### Actor and Component

二者都继承于Uobject,actor在设计概念上有点类似于容器，它身上放置了一堆的component来实现各种功能，如在3d世界里面放置，实现actor的父子关系等各种功能，有两个容器存放各种的component，一个是set容器存放所有的component,一个是arrary数组存放实例化后的component.

actorcomponent是不能互相嵌套的，只有在scenecomponent上面才提供了嵌套功能，这点可以在ue的蓝图中就可以看出来。

### Level && World

#### Level

![1712467710661](image/README/1712467710661.png)

  可以看到level也是继承于uobject的，而在这副图的内容也很丰富，可以看到actor们全都存储在level里面，且继承自Ainfo的worldSettings放在Actors里面的第一个位置上，理由是非网络的actor放在前面，网络复制的actor放在后面，这相当于给网络复制的actor划分了缓存，加速了网络复制时的检测速度。

  在这张图中我们可以看到与Ulevel密切相关的两个actor，一个是AlevelScriptActor，它是关卡蓝图的父类。一个是继承自AInfo这种没有scenecomponent的AWorldSettings。

#### World

有两种方式操纵level和world使他们互相之间转变，一种是sublevel，一种是worldcomposition.具体实现不追究，观其本质，就是一个world有多个level，这些Level在什么位置，是在一开始就加载进来，还是Streaming运行时加载。

UE里每个World支持一个PersistentLevel（持久关卡）和多个其他Level：

![1712469172424](image/README/1712469172424.png)

Persistent的意思是一开始就加载进World，Streaming是后续动态加载的意思。Levels里保存有所有的当前已经加载的Level，StreamingLevels保存整个World的Levels配置列表。

PersistentLevel和CurrentLevel只是个快速引用。在编辑器里编辑的时候，CurrentLevel可以指向其他Level，但运行时CurrentLevel只能是指向PersistentLevel。

每一个world都有一个主PersistentLevel，它的意思是关于整个世界的配置可以在主level里的worldsettings里面设置，但是每一个level里面的个性化设置可以到单独的level里面设置。

### WorldContext,GameInstance,Engine

#### WorldContext

什么是worldContext呢？UE用来管理和跟踪这些World的工具。在这句话中你可以看到它没有保存World。但同样也透漏出一点消息就是world并不是唯一的，而worldcontext是唯一的。

![1712558238854](image/README/1712558238854.png)

上面的结论可以在这段代码中看出。Fworldcontext里存了一个Uworld的指针，指向当前的world.而当需要从一个World切换到另一个World的时候（比如说当点击播放时，就是从Preview切换到PIE），FWorldContext就用来保存切换过程信息和目标World上下文信息.而且level的切换也是在Fworld里面的。

为什么level的切换不放在world里面呢？

这是因为一个world只有一个主persistentlevel，当我们切换该level的时候，ue要释放当前world,然后加载下一个拥有主level的world。假如把level的切换放到world里，那就意味着你当前的world必须保存着下一个world的主level,这与引擎的设计不符合，不符合一个world拥有一个主level的设计。好，我们当然也可以将下一个world的主level当作普通level存放在当前的world，但是我们释放world的时候就不得不把当前的world放在一个临时对象中，相比直接加载world，这个损耗会大。

#### GameInstace

从前面的学习中我们知道，所有的component存放在actor的一个set容器里面，所有的actor存放在level里面，所有的level存放在world里，当前的world存放在FworldContext里面，那么FworldContext又存放在了哪里呢？

![1712559359411](image/README/1712559359411.png)

可以从代码里面看到，UGameInstace存放着Fworldcontext的指针，因为Fworldcontext是唯一的，所以指针就够了，不用数组。当然，UGameInstance不仅仅存放这一个东西，它存放着独立于level的的逻辑和数据。

#### Engine

![1712559606427](image/README/1712559606427.png)

可以从这张图片中看到，UGameEngine存放着一个UGameInstance的指针，这说明了UGameInstance也是唯一的，因为ue不支持同时运行多个world.

在UEngine里面可以看到它存放着一个FworldContext的数组，但是Fworldcontext不是唯一的吗，为什么他会有一个数组呢。在我看来，应该是FworldContext的指针指向一个world他就将该world放在这个数组里面，这样他就可以保存所有的world了。

UEngine上就剩下一个GEngine了，也就是根源了。至此，ue游戏世界的各个部分清楚了。

Object->Actor+Component->Level->World->WorldContext->GameInstance->Engine。

### pawn
