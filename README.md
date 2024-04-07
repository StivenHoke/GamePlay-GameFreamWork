# GamePlay-GameFreamWork

## UEGameFreamWork

### Actor and Component

二者都继承于Uobject,actor在设计概念上有点类似于容器，它身上放置了一堆的component来实现各种功能，如在3d世界里面放置，实现actor的父子关系等各种功能，有两个容器存放各种的component，一个是set容器存放所有的component,一个是arrary数组存放实例化后的component.

actorcomponent是不能互相嵌套的，只有在scenecomponent上面才提供了嵌套功能，这点可以在ue的蓝图中就可以看出来。
