# Android Open Project Renovation  
https://github.com/jadepeakpoet/AndroidProjectRenovation  
# The official original author of vlayout has announced to stop maintenance. The fork has made its own updates and modifications. At present, it mainly modifies the following functions. If you have any problems, please contact me with issue at project AndroidProjectRenovation  
1. Migrate to androidx  
2. Upgrade plug-ins and dependent libraries  
3. Publish the library to jitpack  
4. Fix the problem of margin and padding display error caused by page scrolling from bottom to top after LinearLayoutHelper sets margin and padding  
5. Reference method: implementation 'com.github.jadepeakpoet:vlayout:1.0.0'  

# vlayout

[中文文档](README-ch.md)

## Projects of Tangram

### Android

+ [Tangram-Android](https://github.com/alibaba/Tangram-Android)
+ [Virtualview-Android](https://github.com/alibaba/Virtualview-Android)
+ [vlayout](https://github.com/alibaba/vlayout)
+ [UltraViewPager](https://github.com/alibaba/UltraViewPager)

### iOS

+ [Tangram-iOS](https://github.com/alibaba/Tangram-iOS)
+ [Virtualview-iOS](https://github.com/alibaba/VirtualView-iOS)
+ [LazyScrollView](https://github.com/alibaba/lazyscrollview)

Project `vlayout` is a powerful LayoutManager extension for RecyclerView, it provides a group of layouts for RecyclerView. Make it able to handle a complicate situation when grid, list and other layouts in the same recyclerview.

## Design

By providing a custom LayoutManager to RecyclerView, VirtualLayout is able to layout child views with different style at single view elegantly. The custom LayoutManager manages a serial of layoutHelpers where each one implements the specific layout logic for a certain position range items. By the way, implementing your custom layoutHelper and provding it to the framework is also supported.

## Main Feature
* Provide default common layout implementation, decouple the View and Layout. Default layout implementations are:
	* LinearLayoutHelper: provide linear layout as LinearLayoutManager.
	* GridLayoutHelper: provide grid layout as GridLayoutManager, but with more feature.
	* FixLayoutHelper: fix the view at certain position of screen, the view does not scroll with whole page.
	* ScrollFixLayoutHelper: fix the view at certain position of screen, but the view does not show until it scrolls to it position.
	* FloatLayoutHelper: float the view on top of page, user can drag and drop it.
	* ColumnLayoutHelper: perform like GridLayoutHelper but layouts all child views in one line.
	* SingleLayoutHelper: contain only one child view.
	* OnePlusNLayoutHelper: a custom layout with one child view layouted at left and the others at right, you may not need this.
	* StickyLayoutHelper: scroll the view when its position is inside the screen, but fix the view at start or end when its position is outside the screen.
	* StaggeredGridLayoutHelper: provide waterfall like layout as StaggeredGridLayoutManager.
* LayoutHelpers provided by default can be generally divided into two categories. One is non-fix LayoutHelper such as LinearLayoutHelper, GridLayoutHelper, etc which means the children of these LayoutHelper will be layouted in the flow of parent container and will be scrolled with the container scrolling. While the other is fix LayoutHelper which means the child of these is always fix in parent container.


## Usage

### Import Library

Please find the latest version in [release notes](https://github.com/jadepeakpoet/vlayout/releases). The newest version has been upload to jitpack, make sure you have added the repositories. As follow:

For gradle:
``` gradle
implementation 'com.github.jadepeakpoet:vlayout:1.0.0'
```

Or in maven:  
pom.xml
``` xml
<dependency>
  <groupId>com.github.jadepeakpoet</groupId>
  <artifactId>vlayout</artifactId>
  <version>1.0.0</version>
  <type>aar</type>
</dependency>
```

### Initialize LayoutManager
``` java
final RecyclerView recyclerView = (RecyclerView) findViewById(R.id.recycler_view);
final VirtualLayoutManager layoutManager = new VirtualLayoutManager(this);

recyclerView.setLayoutManager(layoutManager);
```

### Initialize recycled pool's size
Provide a reasonable recycled pool's size to your recyclerView, since the default value may not meet your situation and cause re-create views when scrolling.

``` java
RecyclerView.RecycledViewPool viewPool = new RecyclerView.RecycledViewPool();
recyclerView.setRecycledViewPool(viewPool);
viewPool.setMaxRecycledViews(0, 10);
```

**Attention: the demo code above only modify the recycle pool size of item with type = 0, it you has more than one type in your adapter, you should update recycle pool size for each type.**

### Set Adapters

* You can use `DelegateAdapter` for as a root adapter to make combination of your own adapters. Just make it extend ```DelegateAdapter.Adapter``` and overrides ```onCreateLayoutHelper``` method.

``` java
DelegateAdapter delegateAdapter = new DelegateAdapter(layoutManager, hasConsistItemType);
recycler.setAdapter(delegateAdapter);

// Then you can set sub- adapters

delegateAdapter.setAdapters(adapters);

// or
CustomAdapter adapter = new CustomAdapter(data, new GridLayoutHelper());
delegateAdapter.addAdapter(adapter);

// call notify change when data changes
adapter.notifyDataSetChanged();

```

**Attention: When `hasConsistItemType = true`, items with same type value in different sub-adapters share the same type, their view would be reused during scroll. When `hasConsistItemType = false`, items with same type value in different sub-adapters do not share the same type internally.**

* The other way to set adapter is extending ```VirtualLayoutAdapter``` and implementing it to make deep combination to your business code.

``` java
public class MyAdapter extends VirtualLayoutAdapter {
   ......
}

MyAdapter myAdapter = new MyAdapter(layoutManager);

//create layoutHelper list
List<LayoutHelper> helpers = new LinkedList<>();
GridLayoutHelper gridLayoutHelper = new GridLayoutHelper(4);
gridLayoutHelper.setItemCount(25);
helpers.add(gridLayoutHelper);

GridLayoutHelper gridLayoutHelper2 = new GridLayoutHelper(2);
gridLayoutHelper2.setItemCount(25);
helpers.add(gridLayoutHelper2);

//set layoutHelper list to adapter
myAdapter.setLayoutHelpers(helpers);

//set adapter to recyclerView
recycler.setAdapter(myAdapter);

```

In this way, one thing you should note is that you should call ```setLayoutHelpers``` when the data of Adapter changes.

### Config proguard

Add following configs in your proguard file if your app is released with proguard.

```
-keepattributes InnerClasses
-keep class com.alibaba.android.vlayout.ExposeLinearLayoutManagerEx { *; }
-keep class androidx.recyclerview.widget.RecyclerView$LayoutParams { *; }
-keep class androidx.recyclerview.widget.RecyclerView$ViewHolder { *; }
-keep class androidx.recyclerview.widget.ChildHelper { *; }
-keep class androidx.recyclerview.widget.ChildHelper$Bucket { *; }
-keep class androidx.recyclerview.widget.RecyclerView$LayoutManager { *; }
```

# Demo

![](http://img3.tbcdn.cn/L1/461/1/1b9bfb42009047f75cee08ae741505de2c74ac0a)

[Demo Project](https://github.com/jadepeakpoet/vlayout/tree/master/examples)

# FAQ

Read FAQ(In Chinese language only now) before submitting issue: [FAQ](docs/VLayoutFAQ.md)。

# Layout Attributes

Each layoutHelper has a few attributes to control its layout style. See [this](docs/ATTRIBUTES.md) to read more.

# Contributing

Before you open an issue or create a pull request, please read [Contributing Guide](CONTRIBUTING.md) first.

# LICENSE

Vlayout is available under the MIT license.
