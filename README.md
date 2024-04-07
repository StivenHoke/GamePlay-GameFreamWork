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
