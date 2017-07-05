---
title: 百度地图SDK
date: 2016-03-27 23:18:12
tags: android
category: Android

---


# 百度地图 Sample 学习总结

## Demo名称：MapFragment使用  文件名：  MapFragmentDemo.java
### 关键代码：

	MapStatus ms = new MapStatus.Builder().overlook(-20).zoom(15).build();
	BaiduMapOptions bo = new BaiduMapOptions().mapStatus(ms).compassEnabled(false).zoomControlsEnabled(false);
	map = SupportMapFragment.newInstance(bo);
	FragmentManager manager = getSupportFragmentManager();
	manager.beginTransaction().add(R.id.map, map, "map_fragment").commit();

### API MapStatus 
>定义地图状态

<!--more-->

构建者模式构建对象 MapStatus.Builder

字段：

- LatLngBounds bound：当前屏幕显示范围内的地理范围，当旋转或俯视时，是当前屏幕可见显示范围的最大外接矩形
- static CREATOR
- float overlook： 地图俯仰角度。
- float rotate： 地图旋转角度。
- LatLng target： 地图操作的中心点。 （设置地图操作中心点在屏幕的坐标, 只有在 OnMapLoadedCallback.onMapLoaded() 之后设置才生效）
- Point targetScreen： 地图操作中心点在屏幕中的坐标
- float zoom： 地图缩放级别 3~21

### API BaiduMapOptions 
>MapView 初始化选项

设置一些MapView的属性
![BaiduMapOptions可设置的内容](http://i.imgur.com/E6BOfxm.png)

### API SupportFragment

![SupportFragment](http://i.imgur.com/AyfthfW.png)

通过静态方法构建 `SupportMapFragment`对象

## Demo名称：图层展示功能 文件名：  LayersDemo.java
>在普通图、卫星图上可以叠加交通流量图和百度城市热力图

关键代码：

	mBaiduMap.setMapType(BaiduMap.MAP_TYPE_NORMAL);
	mBaiduMap.setMapType(BaiduMap.MAP_TYPE_SATELLITE);
	mBaiduMap.setTrafficEnabled(((CheckBox) v).isChecked());
	mBaiduMap.setBaiduHeatMapEnabled(((CheckBox) view).isChecked());

1.2 二选一，3.4随便

## Demo名称：多地图展示 文件名：  MultiMapViewDemo.java

关键代码：

	MapStatusUpdate u1 = MapStatusUpdateFactory.newLatLng(GEO_BEIJING);
	SupportMapFragment map1 = (SupportMapFragment) getSupportFragmentManager().findFragmentById(R.id.map1);
	map1.getBaiduMap().setMapStatus(u1);
	map1.getMapView().setLogoPosition(LogoPosition.logoPostionleftTop);


布局文件是静态注册fragment：

	class="com.baidu.mapapi.map.SupportMapFragment"
### MapStatusUpdate
>描述地图状态将要发生的变化

### API MapStatusUpdateFactory
>生成地图状态将要发生的变化

![MapStatusUpdateFactory](http://i.imgur.com/ZFeByCy.png)

## Demo名称：地图操作功能 文件名：  MapControlDemo.java

>简介：介绍平移和缩放地图，双指操作地图，监听地图点击事件
详述：

>（1）介绍地图缩放级别、旋转度和俯视度的get和set方法；
>（2）监听单击和长按地图事件；
>（3）单击、双击和长按地图获取该点的经纬度坐标；
>（4）对地图显示内容进行截图，截图保存地址为：/mnt/sdcard/test.png；

地图变化关键代码：

    /**
     * 处理俯视 俯角范围： -45 ~ 0 , 单位： 度
     */
    private void perfomOverlook() {
        EditText t = (EditText) findViewById(R.id.overlookangle);
        try {
            int overlookAngle = Integer.parseInt(t.getText().toString());
            MapStatus ms = new MapStatus.Builder(mBaiduMap.getMapStatus()).overlook(overlookAngle).build();
            MapStatusUpdate msu = MapStatusUpdateFactory.newMapStatus(ms);
            mBaiduMap.animateMapStatus(msu);
        } catch (NumberFormatException e) {
            Toast.makeText(MapControlDemo.this, "请输入正确的俯角", Toast.LENGTH_SHORT).show();
        }

    }

    /**
     * 处理旋转 旋转角范围： -180 ~ 180 , 单位：度 逆时针旋转
     */
    private void perfomRotate() {
        EditText t = (EditText) findViewById(R.id.rotateangle);
        try {
            int rotateAngle = Integer.parseInt(t.getText().toString());
            MapStatus ms = new MapStatus.Builder(mBaiduMap.getMapStatus()).rotate(rotateAngle).build();
            MapStatusUpdate u = MapStatusUpdateFactory.newMapStatus(ms);
            mBaiduMap.animateMapStatus(u);
        } catch (NumberFormatException e) {
            Toast.makeText(this, "请输入正确的旋转角度", Toast.LENGTH_SHORT).show();
        }
    }

    /**
     * 处理缩放
     */
    private void perfomZoom() {
        EditText t = (EditText) findViewById(R.id.zoomlevel);
        try {
            float f = Float.parseFloat(t.getText().toString());
            MapStatusUpdate u = MapStatusUpdateFactory.zoomTo(f);
            mBaiduMap.animateMapStatus(u);
        } catch (NumberFormatException e) {
            Toast.makeText(MapControlDemo.this, "请输入正确的缩放级别", Toast.LENGTH_SHORT).show();
        }
    }

将状态输出：**注意写法**

    /**
     * 更新地图状态显示面板
     */
    private void updateMapState() {
        if (mStateBar == null) {
            return;
        }
        String state = "";
        if (currentPt == null) {


            state = "点击、长按、双击地图以获取经纬度和地图状态";
        } else {
            state = String.format(touchType + ",单前经度：%f 当前纬度： %f", currentPt.longitude, currentPt.latitude);
        }
        state += "\n";
        MapStatus ms = mBaiduMap.getMapStatus();
        state += String.format("zoom= %.1f rotate=%f overlook=%f", ms.zoom, ms.rotate, ms.overlook);
        mStateBar.setText(state);
    }



### API BaiduMap
>定义 BaiduMap 地图对象的操作方法与接口


接口：

![BaiduMap](http://i.imgur.com/5szZ5Wj.png)


### API LatLng
>地理坐标基本数据结构

- double	latitude 纬度
- double	latitudeE6
- double	longitude 经度
- double	longitudeE6 

接口中的点击监听都可以得到这个对象。获得点击的经纬度。

## Demo名称：UI控制功能 文件名：  UISettingsDemo.java --未看

## Demo名称：定位图层展示 文件名：  LocationOverlayDemo.java

###关键代码
#### 配置定位图层显示方式
	mCurrentMode = MyLocationConfiguration.LocationMode.NORMAL;
	mBaiduMap.setMyLocationConfigeration(new MyLocationConfiguration(mCurrentMode, true, mCurrentMarker));

#### 配置定位图层的自定义图标和光圈
	mBaiduMap.setMyLocationConfigeration(new MyLocationConfiguration(mCurrentMode, true, mCurrentMarker, accuracyCircleFillColor, accuracyCircleStrokeColor));
	mBaiduMap.setMyLocationConfigeration(new MyLocationConfiguration(mCurrentMode, true, null)); // 默认图标 默认进度圈风格

#### 开启定位
1. 开启定位图层

		mBaiduMap.setMyLocationEnabled(true);
2. 定位初始化：通过LocalClient定位，监听器方法中返回定位结果

		mLocClient = new LocationClient(this);
        mLocClient.registerLocationListener(myListener);
        LocationClientOption option = new LocationClientOption();
        option.setOpenGps(true);
        option.setCoorType("bd0911"); // 坐标类型
        option.setScanSpan(1000);
        mLocClient.setLocOption(option);
        mLocClient.start();
3. 获取监听器方法中的定位结果内容设置到`LocationData`对象里面，最后将`LocationData`对象设置到`mBaiduMap`对象中在地图上显示

	    public class MyLocationListener implements BDLocationListener {
	
	        @Override
	        public void onReceiveLocation(BDLocation bdLocation) {
	            // map view 销毁后不在处理新接收的位置
	            if (bdLocation == null || mMapView == null) {
	                return;
	            }
	            MyLocationData locData = new MyLocationData.Builder()
	                    .accuracy(bdLocation.getRadius())
	                    // 此处设置开发者获取到的方向信息，顺时针0-360
	                    .direction(100).latitude(bdLocation.getLatitude())
	                    .longitude(bdLocation.getLongitude()).build();
	            mBaiduMap.setMyLocationData(locData);
	            if (isFirstLoc) {
	                isFirstLoc = false;
	                LatLng ll = new LatLng(bdLocation.getLatitude(),
	                        bdLocation.getLongitude());
	                MapStatus.Builder builder = new MapStatus.Builder();
	                builder.target(ll).zoom(18.0f);
	                mBaiduMap.animateMapStatus(MapStatusUpdateFactory.newMapStatus(builder.build()));
	            }
	        }
	
	    }

#### 销毁定位释放资源
>停止定位服务，关闭定位图层

    @Override
    protected void onDestroy() {
        // 退出时销毁定位
        mLocClient.stop();
        // 关闭定位图层
        mBaiduMap.setMyLocationEnabled(false);
        mMapView.onDestroy();
        mMapView = null;
        super.onDestroy();
    }

### API　MyLocationConfiguration
>配置定位图层显示方式  

![MyLocationConfiguration 内部类](http://i.imgur.com/3NAjT6G.png)

![MyLocationConfiguration 构造方法](http://i.imgur.com/scroOay.png)

配置内容只有5项：

![MyLocationConfiguration 普通方法](http://i.imgur.com/hQbtoAR.png)

### API MyLocationConfiguration.LocationMode
> 枚举 定位图层显示方式

- COMPASS 罗盘态，显示定位方向圈，保持定位图标在地图中心
- FOLLOWING 跟随态，保持定位图标在地图中心
- NORMAL 普通态： 更新定位数据时不对地图做任何操作

获取常量数组

	MyLocationConfiguration.LocationMode[] = MyLocationConfiguration.LocationMode.values() 

	MyLocationConfiguration.LacationMode mCurrentMode = MyLocationConfiguration.LacationMode.COMPASS;
### API　MyLocationData MyLocationData.Builder
>定位数据 构建者方式构建该对象

可设置内容：

![MyLocationData](http://i.imgur.com/u5hQ0NJ.png)


### API MyLocationData.Builder
>定位数据建造器

## Demo名称：自定义绘制功能 文件名：  GeometryDemo.java
>简介：介绍自定义绘制点、线、多边形、圆等几何图形和文字
详述：
（1）支持绘制凸多边形，如要绘制凹多边形请用三角形进行拼接；
（2）支持绘制文字；

## Demo名称：覆盖物功能 文件名：  OverlayDemo.java
>简介：介绍添加覆盖物并响应点击功能和弹出pop功能
详述：
（1）在popup中添加弹出自定义View的示例；
（2）点击周围4个Marker，弹出popup,可更新marker位置和更新marker图标；
（3）中间显示的为图片图层，该图层可随地图的移动、缩放等做出相应的操作；
（4）支持Maker设置透明度的方法，利用滑动条可以展示maker透明度变化；
（5）支持Marker设置动画的能力，包含两种方式：从地上生长和从天上落下

### 关键代码

	MarkerOptions ooA = new MarkerOptions().position(llA).icon(bdA).zIndex(9).draggable(true);
	if (animationBox.isChecked()) {
		// 掉下动画
		ooA.animateType(MarkerAnimateType.drop);
	}
	mMarkerA = (Marker) (mBaiduMap.addOverlay(ooA));

#### 更新地图范围
	// add ground overlay
	LatLng southwest = new LatLng(39.92235, 116.380338);
	LatLng northeast = new LatLng(39.947246, 116.414977);
	LatLngBounds bounds = new LatLngBounds.Builder().include(northeast).include(southwest).build();

	OverlayOptions ooGround = new GroundOverlayOptions().positionFromBounds(bounds).image(bdGround).transparency(0.8f);
	mBaiduMap.addOverlay(ooGround);

	MapStatusUpdate u = MapStatusUpdateFactory.newLatLng(bounds.getCenter());
	mBaiduMap.setMapStatus(u);

#### 结束
    @Override
    protected void onDestroy() {
        // MapView的生命周期与Activity同步，当activity销毁时需调用MapView.destroy()
        mMapView.onDestroy();
        super.onDestroy();
        // 回收 bitmap 资源
        bdA.recycle();
        bdB.recycle();
        bdC.recycle();
        bdD.recycle();
        bd.recycle();
        bdGround.recycle();
    }


### Marker
> 定义地图 Marker 覆盖物  extends Overlay

### MarkerOptions

### MarkerOptions.MarkerAnimateType
>marker添加动画，目前支持掉下和生长两种

- drop 从天上掉下
- grow 从地面生长
- none 没效果

### Overlay
>子类Arc, Circle, Dot, GroundOverlay, Marker, Polygon, Polyline, Text

### OverlayOptions
>地图覆盖物选型基类
 
### LatLngBounds  LatLngBounds.Builder
>地理范围数据结构，由西南以及东北坐标点确认



## Demo名称：热力图功能 文件名：  HeatMapDemo.java
>简介绘制自有数据热力图
详述：
（1）设置热力图颜色；
（2）准备数据、生成热力图；
（3）删除热力图；

##　Demo名称：地理编码功能　文件名：  GeoCodeDemo.java
>简介：介绍地址信息与坐标之间的相互转换
详述：
（1）正向地理编码：将地址信息转换为经纬度坐标；
（2）反向地理编码：将经纬度坐标转换为地址信息；

## Demo名称：POI搜索功能 文件名：  POISearchDemo.java
>简介：介绍关键词查询、suggestion查询和查看餐饮类Place详情页功能
详述：
（1）点击某些关键词查询后的结果（如“餐厅”）可跳转到Place详情页；
（2）提供suggestion查询进行联想查询，例如输入“天安门”则会弹出联想查询的列表；

### 初始化搜索模块，注册监听事件

	mPoiSearch = PoiSearch.newInstance();
	mPoiSearch.setOnGetPoiSearchResultListener(this);
    mSuggestionSearch = SuggestionSearch.newInstance();
    mSuggestionSearch.setOnGetSuggestionResultListener(this);
   
### 获取静态注册的SupportMapFragment对象
	mBaiduMap = ((SupportMapFragment) getSupportFragmentManager().findFragmentById(R.id.map)).getBaiduMap();

### 开启建议请求
	mSuggestionSearch.requestSuggestion(new SuggestionSearchOption().keyword(s.toString()).city(city));

### 建议请求结果处理

    @Override
    public void onGetSuggestionResult(SuggestionResult suggestionResult) {
        if (suggestionResult == null || suggestionResult.getAllSuggestions() == null) {
            return;
        }
        suggest = new ArrayList<String>();
        for (SuggestionResult.SuggestionInfo info : suggestionResult.getAllSuggestions()) {
            if (info.key != null) {
                suggest.add(info.key);
            }
        }
        sugAdapter = new ArrayAdapter<String>(PoiSearchDemo.this, android.R.layout.simple_dropdown_item_1line, suggest);
        keyWorldsView.setAdapter(sugAdapter);
        sugAdapter.notifyDataSetChanged();
    }

### 开启搜索

	mPoiSearch.searchInCity(new PoiCitySearchOption()
                .city(editCity.getText().toString())
                .keyword(editSearchKey.getText().toString())
                .pageNum(loadIndex));

参数：

- `PoiCitySearchOption`
- `PoiDetailSearchOption`
- `PoiBoundSearchOption`
- `PoiNearbySearchOption`
### 监听处理搜索结果

    @Override
    public void onGetPoiResult(PoiResult poiResult) {
        if (poiResult == null || poiResult.error == SearchResult.ERRORNO.RESULT_NOT_FOUND) {
            Toast.makeText(PoiSearchDemo.this, "为找到结果", Toast.LENGTH_SHORT).show();
            return;
        }
        if (poiResult.error == SearchResult.ERRORNO.NO_ERROR) {
            //mBaiduMap.clear();
            //MyPoiOverlay overlay = new MyPoiOverlay(mBaiduMap);
            //mBaiduMap.setOnMarkerClickListener(overlay);
            //overlay.setDate(poiResult);
            //overlay.addToMap();
            //overlay.zoomToSpan();
			
            return;
        }
        if (poiResult.error == SearchResult.ERRORNO.AMBIGUOUS_KEYWORD) {
            // 当输入关键字在本市没有找到，但在其他城市找到时，返回包含该关键字信息的城市列表
            String strInfo = "在";
            for (CityInfo cityInfo : poiResult.getSuggestCityList()) {
                strInfo += cityInfo.city;
                strInfo += ",";
            }
            strInfo += "找到结果";
            Toast.makeText(PoiSearchDemo.this, strInfo, Toast.LENGTH_SHORT).show();
        }
    }

    @Override
    public void onGetPoiDetailResult(PoiDetailResult poiDetailResult) {
        if (poiDetailResult.error != SearchResult.ERRORNO.NO_ERROR) {
            Toast.makeText(PoiSearchDemo.this, "抱歉，未找到结果", Toast.LENGTH_SHORT)
                    .show();
        } else {
            Toast.makeText(PoiSearchDemo.this, poiDetailResult.getName() + ": " + result.getAddress(), Toast.LENGTH_SHORT)
                    .show();
        }
    }

### 结尾

    @Override
    protected void onDestroy() {
        mPoiSearch.destroy();
        mSuggestionSearch.destroy();
        super.onDestroy();
    }

### API PoiSearch
>POI检索接口

![PoiSearch](http://i.imgur.com/BAosBvE.png)

有三种搜索方式：

- 范围内搜索
- 城市内搜索
- 周边搜索
- POI详情检索

### API SuggestionSearch SuggestionSearchOption SuggestionResult SuggestionResult.SuggestionInfo
>建议查询接口 建议查询请求参数

#### SuggestionSearch

![SuggestionSearch](http://i.imgur.com/awDzsF0.png)

#### SuggestionSearchOption

![SuggestionSearchOption](http://i.imgur.com/NC5jbN1.png)

`SuggestionSearch`对象执行方法`requestSuggestion(SuggestionSearchOption对象)`设置查询参数，在监听方法中获取建议查询结果

#### SuggestionResult
![SuggestionResult](http://i.imgur.com/cGNMZvL.png)

#### SuggestionResult.SuggestionInfo:

![SuggestionResult.SuggestionInfo](http://i.imgur.com/dOLGREG.png)

## Demo名称：路径规划功能
>文件名：  RoutePlanDemo.java, CustomRouteOverlay.java

>简介：介绍公交、驾车、骑行和步行三种线路规划方法和自设路线方法
详述：
（1）驾车查询新增路径点查询功能，具体使用方法详见开发者指南路径规划部分，只需重载接口；
（2）自设路线功能演示开发者如何自己设定一条路线，包括如何设定起点、终点、途径站点和路段；
（3）自设路线功能同时也介绍如何在两个Activity之间切换的时候管理Mapview的生命周期；
（4）可自定义路线的起终点图标；

### 关键代码

#### 初始化搜索模块，注册事件监听
	mSearch = RoutePlanSearch.newInstance();
	mSearch.setOnGetRoutePlanResultListener(this);

#### 发起路线规划搜索示例

    public void searchButtonProcess(View v) {
        // 重置浏览节点的路线数据
        route = null;
        mBtnPre.setVisibility(View.INVISIBLE);
        mBtnNext.setVisibility(View.INVISIBLE);
        mBaidumap.clear();
        // 处理搜索按钮响应
        EditText editSt = (EditText) findViewById(R.id.start);
        EditText editEn = (EditText) findViewById(R.id.end);
        // 设置起终点信息，对于tranist search 来说，城市名无意义
        PlanNode stNode = PlanNode.withCityNameAndPlaceName("北京", editSt.getText().toString());
        PlanNode enNode = PlanNode.withCityNameAndPlaceName("北京", editEn.getText().toString());

        // 实际使用中请对起点终点城市进行正确的设定
        if (v.getId() == R.id.drive) {
            mSearch.drivingSearch((new DrivingRoutePlanOption())
                    .from(stNode).to(enNode));
        } else if (v.getId() == R.id.transit) {
            mSearch.transitSearch((new TransitRoutePlanOption())
                    .from(stNode).city("北京").to(enNode));
        } else if (v.getId() == R.id.walk) {
            mSearch.walkingSearch((new WalkingRoutePlanOption())
                    .from(stNode).to(enNode));
        } else if (v.getId() == R.id.bike) {
            mSearch.bikingSearch((new BikingRoutePlanOption())
                    .from(stNode).to(enNode));
        }
    }

#### 节点浏览示例

    public void nodeClick(View v) {
        if (route == null || route.getAllStep() == null) {
            return;
        }
        if (nodeIndex == -1 && v.getId() == R.id.pre) {
            return;
        }
        // 设置节点索引
        if (v.getId() == R.id.next) {
            if (nodeIndex < route.getAllStep().size() - 1) {
                nodeIndex++;
            } else {
                return;
            }
        } else if (v.getId() == R.id.pre) {
            if (nodeIndex > 0) {
                nodeIndex--;
            } else {
                return;
            }
        }
        // 获取节结果信息
        LatLng nodeLocation = null;
        String nodeTitle = null;
        Object step = route.getAllStep().get(nodeIndex);
        if (step instanceof DrivingRouteLine.DrivingStep) {
            nodeLocation = ((DrivingRouteLine.DrivingStep) step).getEntrance().getLocation();
            nodeTitle = ((DrivingRouteLine.DrivingStep) step).getInstructions();
        } else if (step instanceof WalkingRouteLine.WalkingStep) {
            nodeLocation = ((WalkingRouteLine.WalkingStep) step).getEntrance().getLocation();
            nodeTitle = ((WalkingRouteLine.WalkingStep) step).getInstructions();
        } else if (step instanceof TransitRouteLine.TransitStep) {
            nodeLocation = ((TransitRouteLine.TransitStep) step).getEntrance().getLocation();
            nodeTitle = ((TransitRouteLine.TransitStep) step).getInstructions();
        } else if (step instanceof BikingRouteLine.BikingStep) {
            nodeLocation = ((BikingRouteLine.BikingStep) step).getEntrance().getLocation();
            nodeTitle = ((BikingRouteLine.BikingStep) step).getInstructions();
        }

        if (nodeLocation == null || nodeTitle == null) {
            return;
        }
        // 移动节点至中心
        mBaidumap.setMapStatus(MapStatusUpdateFactory.newLatLng(nodeLocation));
        // show popup
        popupText = new TextView(RoutePlanDemo.this);
        popupText.setBackgroundResource(R.drawable.popup);
        popupText.setTextColor(0xFF000000);
        popupText.setText(nodeTitle);
        mBaidumap.showInfoWindow(new InfoWindow(popupText, nodeLocation, 0));

    }

#### 切换路线图标
    /**
     * 切换路线图标，刷新地图使其生效
     * 注意： 起终点图标使用中心对齐.
     */
    public void changeRouteIcon(View v) {
        if (routeOverlay == null) {
            return;
        }
        if (useDefaultIcon) {
            ((Button) v).setText("自定义起终点图标");
            Toast.makeText(this,
                    "将使用系统起终点图标",
                    Toast.LENGTH_SHORT).show();

        } else {
            ((Button) v).setText("系统起终点图标");
            Toast.makeText(this,
                    "将使用自定义起终点图标",
                    Toast.LENGTH_SHORT).show();

        }
        useDefaultIcon = !useDefaultIcon;
        routeOverlay.removeFromMap();
        routeOverlay.addToMap();
    }

####　结束

    @Override
    protected void onDestroy() {
        mSearch.destroy();
        mMapView.onDestroy();
        super.onDestroy();
    }
### API RoutePlanSearch
>路径规划搜索接口

![RoutePlanSearch](http://i.imgur.com/XxQCPA2.png)


### PlanNode
路径规划中的出行节点信息,出行节点包括：起点，终点，途经点
出行节点信息可以通过两种方式确定：

1. 给定出行节点经纬度坐标

2. 给定出行节点地名和城市名

![](http://i.imgur.com/Sr2OLIY.png)


## Demo名称：公交线路查询功能
>文件名：  BusLineDemo.java

>简介：介绍查询公交线路功能
（1）点击“开始”按钮可查询公交线路；
（2）搜索出公交线路后点击“下一条”按钮可查询该线路的反方向公交线路；

### 关键代码

#### 发起一个POI检索

    /**
     * 发起检索
     *
     * @param v
     */
    public void searchButtonProcess(View v) {
        busLineIDList.clear();
        busLineIndex = 0;
        mBtnPre.setVisibility(View.INVISIBLE);
        mBtnNext.setVisibility(View.INVISIBLE);
        EditText editCity = (EditText) findViewById(R.id.city);
        EditText editSearchKey = (EditText) findViewById(R.id.searchkey);
        // 发起poi检索，从得到所有poi中找到公交线路类型的poi，再使用该poi的uid进行公交详情搜索
        mSearch.searchInCity((new PoiCitySearchOption()).city(
                editCity.getText().toString())
                        .keyword(editSearchKey.getText().toString()));
    }


#### 对POI检索结果过滤找到公交线路的poi，存到id集合中


    @Override
    public void onGetPoiResult(PoiResult result) {

        if (result == null || result.error != SearchResult.ERRORNO.NO_ERROR) {
            Toast.makeText(BusLineSearchDemo.this, "抱歉，未找到结果",
                    Toast.LENGTH_LONG).show();
            return;
        }
        // 遍历所有poi，找到类型为公交线路的poi
        busLineIDList.clear();
        for (PoiInfo poi : result.getAllPoi()) {
            if (poi.type == PoiInfo.POITYPE.BUS_LINE
                    || poi.type == PoiInfo.POITYPE.SUBWAY_LINE) {
                busLineIDList.add(poi.uid); // 添加符合要求的poi的uid
            }
        }
        searchNextBusline(null);
        route = null;
    }

#### ------

    @Override
    public void onGetBusLineResult(BusLineResult result) {
        if (result == null || result.error != SearchResult.ERRORNO.NO_ERROR) {
            Toast.makeText(BusLineSearchDemo.this, "抱歉，未找到结果",
                    Toast.LENGTH_LONG).show();
            return;
        }
        mBaiduMap.clear();
        route = result;
        nodeIndex = -1;
        overlay.removeFromMap();
        overlay.setData(result);
        overlay.addToMap();
        overlay.zoomToSpan();
        mBtnPre.setVisibility(View.VISIBLE);
        mBtnNext.setVisibility(View.VISIBLE);
        Toast.makeText(BusLineSearchDemo.this, result.getBusLineName(),
                Toast.LENGTH_SHORT).show();
    }


#### 结束
    @Override
    protected void onDestroy() {
        mSearch.destroy();
        mBusLineSearch.destroy();
        super.onDestroy();
    }


### BusLineSearch
>城市公交信息(包含地铁信息)查询

### BusLineSearchOption
>城市公交信息查询参数

![BusLineSearchOption](http://i.imgur.com/M7f5JqI.png)
### BusLineResult
>公共交通信息查询结果

### BusLineResult.BusStep

### BusLineResult.BusStep

### PoiInfo
>poi信息类

### PoiInfo.POITYPE
poi类型，0：普通点，1：公交站，2：公交线路，3：地铁站，4：地铁线路,