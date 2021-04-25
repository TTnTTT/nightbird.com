---
title: 入坟GIS
date: 2021-01-04 10:29:41
categories:
- [技术文章,框架]
tags: 
- WebGIS
- postgreSQL
- postgis
- geotools
---

# Java Geometry空间几何数据的处理应用

## 几个概念

- **地理信息系统GIS**-[百度百科](https://links.jianshu.com/go?to=https%3A%2F%2Fbaike.baidu.com%2Fitem%2F%E5%9C%B0%E7%90%86%E4%BF%A1%E6%81%AF%E7%B3%BB%E7%BB%9F%2F171830%3Ffromtitle%3DGIS%26fromid%3D31541%26fr%3Daladdin) 

> **地理信息系统**（Geographic Information System或 Geo－Information system，GIS）有时又称为“地学信息系统”。它是一种特定的十分重要的空间信息系统。它是在[计算机](https://links.jianshu.com/go?to=https%3A%2F%2Fbaike.baidu.com%2Fitem%2F%E8%AE%A1%E7%AE%97%E6%9C%BA%2F140338)硬、软件系统支持下，对整个或部分[地球](https://links.jianshu.com/go?to=https%3A%2F%2Fbaike.baidu.com%2Fitem%2F%E5%9C%B0%E7%90%83%2F6431)表层（包括大气层）空间中的有关[地理](https://links.jianshu.com/go?to=https%3A%2F%2Fbaike.baidu.com%2Fitem%2F%E5%9C%B0%E7%90%86)分布[数据](https://links.jianshu.com/go?to=https%3A%2F%2Fbaike.baidu.com%2Fitem%2F%E6%95%B0%E6%8D%AE%2F33305)进行[采集](https://links.jianshu.com/go?to=https%3A%2F%2Fbaike.baidu.com%2Fitem%2F%E9%87%87%E9%9B%86%2F4843625)、[储存](https://links.jianshu.com/go?to=https%3A%2F%2Fbaike.baidu.com%2Fitem%2F%E5%82%A8%E5%AD%98%2F2446499)、[管理](https://links.jianshu.com/go?to=https%3A%2F%2Fbaike.baidu.com%2Fitem%2F%E7%AE%A1%E7%90%86%2F366755)、[运算](https://links.jianshu.com/go?to=https%3A%2F%2Fbaike.baidu.com%2Fitem%2F%E8%BF%90%E7%AE%97%2F5866856)、[分析](https://links.jianshu.com/go?to=https%3A%2F%2Fbaike.baidu.com%2Fitem%2F%E5%88%86%E6%9E%90%2F4327108)、[显示](https://links.jianshu.com/go?to=https%3A%2F%2Fbaike.baidu.com%2Fitem%2F%E6%98%BE%E7%A4%BA%2F9985945)和[描述](https://links.jianshu.com/go?to=https%3A%2F%2Fbaike.baidu.com%2Fitem%2F%E6%8F%8F%E8%BF%B0%2F8928757)的技术系统

- **ArcGIS平台**

> ArcGIS产品线为用户提供一个可伸缩的，全面的[GIS](https://links.jianshu.com/go?to=https%3A%2F%2Fbaike.baidu.com%2Fitem%2FGIS%2F31541)平台。ArcObjects包含了许多的可编程组件，从细粒度的对象（例如单个的几何对象）到粗粒度的对象（例如与现有ArcMap文档交互的地图对象）涉及面极广，这些对象为开发者集成了全面的GIS功能。

- **Geometry数据类型**

Geometry是一种空间几何数据类型，常用于描述空间几何信息，例如坐标点、线、面、三维信息等。
 也就是说，GIS一般使用Geometry数据类型来存储及展示地理信息。
 常见的支持Geometry的数据库有Oracle、SqlServer、Mysql、PostgreSQL
 Sql-Server中支持的Geometry[文档](https://links.jianshu.com/go?to=%5Bhttps%3A%2F%2Fdocs.microsoft.com%2Fzh-cn%2Fsql%2Frelational-databases%2Fspatial%2Fspatial-data-sql-server%3Fview%3Dsql-server-2017%5D(https%3A%2F%2Fdocs.microsoft.com%2Fzh-cn%2Fsql%2Frelational-databases%2Fspatial%2Fspatial-data-sql-server%3Fview%3Dsql-server-2017))以及Sql-Server中可选择的地理数据类型如图

- 应用场景示例

  

  ![img](https:////upload-images.jianshu.io/upload_images/10533664-cb3fc98c9966bdef.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

  

   如图，该应用主要为使用ArcGIS做地理信息系统，使用SqlServer作为空间数据库，使用Java搭建后台服务处理数据，使用JS的Vue结合ArcGIS的API做前端的渲染，成功的将地理信息高亮标识在地图上

## 在Java中到底怎么操作

### 1. 使用ArcGIS创建地理数据库，发布GIS服务

[数据库和 ArcGIS Enterprise](https://links.jianshu.com/go?to=%5Bhttps%3A%2F%2Fenterprise.arcgis.com%2Fzh-cn%2Fserver%2Flatest%2Fmanage-data%2Fwindows%2Fdatabases-and-enterprise.htm%5D(https%3A%2F%2Fenterprise.arcgis.com%2Fzh-cn%2Fserver%2Flatest%2Fmanage-data%2Fwindows%2Fdatabases-and-enterprise.htm))
 [如何注册数据库到 ArcGIS Server 站点](https://links.jianshu.com/go?to=%5Bhttps%3A%2F%2Fenterprise.arcgis.com%2Fzh-cn%2Fserver%2Flatest%2Fmanage-data%2Fwindows%2Fregistering-your-data-with-arcgis-server-using-manager.htm%23ESRI_SECTION1_9D5CE649771049C6A0886A79E3C5B8AC%5D(https%3A%2F%2Fenterprise.arcgis.com%2Fzh-cn%2Fserver%2Flatest%2Fmanage-data%2Fwindows%2Fregistering-your-data-with-arcgis-server-using-manager.htm%23ESRI_SECTION1_9D5CE649771049C6A0886A79E3C5B8AC))
 [发布地图服务](https://links.jianshu.com/go?to=%5Bhttps%3A%2F%2Fenterprise.arcgis.com%2Fzh-cn%2Fserver%2Flatest%2Fpublish-services%2Fwindows%2Fwhat-is-a-map-service.htm%5D(https%3A%2F%2Fenterprise.arcgis.com%2Fzh-cn%2Fserver%2Flatest%2Fpublish-services%2Fwindows%2Fwhat-is-a-map-service.htm))
 通过以上的文档，我们可以创建一个地理数据库，并将数据库通过arcgis进行连接，发布为一个Restful服务

------

访问该接口，如图



![img](https:////upload-images.jianshu.io/upload_images/10533664-a2454cc5f0767974.png?imageMogr2/auto-orient/strip|imageView2/2/w/996/format/webp)

image.png

**那这个接口提供了那些功能呢？**
 支持的操作大概如下：
 Supported Operations:   Query   Apply Edits   Add Features   Update Features   Delete Features   Calculate   Validate SQL   Generate Renderer   Return Updates   Iteminfo   Thumbnail   Metadata

**由ArcGIS提供的这些服务，我们可以以可视化的方式，对空间地理信息进行查询及展示**
 后面我们将做进一步的说明

### 2. 空间数据以及WKT熟知文本

好的，我们有了数据库，也有了arcgis提供的服务，现在该如何新增空间数据呢？
 先通过WKT了解下空间数据Geometry大概长什么样

> [WKT](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.baike.com%2Fwiki%2FWKT)，是一种文本标记语言，用于表示矢量几何对象、空间参照系统及空间参照系统之间的转换。它的二进制表示方式，亦即WKB(well-known binary)则胜于在传输和在数据库中存储相同的信息。该格式由开放地理空间联盟(OGC)制定。

> WKT可以表示的几何对象包括：点，线，多边形，TIN（[不规则三角网](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.baike.com%2Fsowiki%2F%E4%B8%8D%E8%A7%84%E5%88%99%E4%B8%89%E8%A7%92%E7%BD%91%3Fprd%3Dcontent_doc_search)）及多面体。可以通过几何集合的方式来表示不同维度的几何对象。
>  几何物体的坐标可以是2D(x,y),3D(x,y,z),4D(x,y,z,m),加上一个属于线性参照系统的m值。
>  以下为几何WKT字串样例：
>  POINT(6 10)
>  LINESTRING(3 4,10 50,20 25)
>  POLYGON((1 1,5 1,5 5,1 5,1 1),(2 2,2 3,3 3,3 2,2 2))
>  MULTIPOINT(3.5 5.6, 4.8 10.5)
>  MULTILINESTRING((3 4,10 50,20 25),(-5 -8,-10 -8,-15 -4))
>  MULTIPOLYGON(((1 1,5 1,5 5,1 5,1 1),(2 2,2 3,3 3,3 2,2 2)),((6 3,9 2,9 4,6 3)))
>  GEOMETRYCOLLECTION(POINT(4 6),LINESTRING(4 6,7 10))
>  POINT ZM (1 1 5 60)
>  POINT M (1 1 80)
>  POINT EMPTY
>  MULTIPOLYGON EMPTY

到这里我们清楚了，所谓**空间数据就是一个或一组坐标**，这个坐标或坐标组有类型（POINT点LINESTRING线POLYGON面），通过这些坐标，GIS系统可以完整地定位查询绘制这些坐标信息。

### 3. 向空间数据库插入数据

那数据库如何新增这些空间数据呢？
 先了解一下SQLSERVER空间数据（当然也可以是其他空间数据库：postgis...）的插入语句长啥样

```bash
--GEOM是类型为Geometry的字段--
--我们向该字段新增了一条3D的多边形数据--
--geometry :: STGeomFromText () 是由SQLSERVER提供的函数，它能将WKT文本转换为数据库geometry类型的数据--
INSERT INTO [dbo].[TEST_GEO_TABLE] ( [GEOM] )
VALUES
    ( geometry :: STGeomFromText ( 
    'POLYGON ((
        113.507259000000005 22.24814946 8, 
        113.507188600000006 22.248088559999999 9, 
        113.507117399999998 22.24802743 10, 
        113.507046099999997 22.24796624 11, 
        113.507017300000001 22.247888209999999 12
        ))',4326 )
    );
```

也就是说，将坐标转化为WKT文本，我们就可以插入空间数据。接下来我们要考虑的是如何产生WKT文本

### 4. 使用Java创建Geometry对象

#### 4.1 常见Geometry的JavaAPI

wkt文本仅仅是一个字符串而已，直接将坐标点拼接成符合WKT格式的字符串不就可以了吗？
 道理是这个道理，要做好可就难了。

- 拼接工作量巨大
- 拼接过程容易出错
- 拼接的结果不一定合法可用
   我们需要一套JAVA API对数据进行处理，能够方便的创建Geometry对象，进行地理信息的绘制、创建、验证等等功能

市面上常见的GeometryApi有

 [Esri](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FEsri)/**geometry-api-java** 

 [locationtech](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Flocationtech)/**jts** （推荐）

Esri是Arcgis官方提供的javaSDK，可惜功能不多，甚至不能提供基本的空间计算功能。
 jts功能较为齐全，资料也相对丰富一点

**点击上面链接可以访问其Github地址，本文将以jts为例进行说明**

#### 4.2 JTS的部分API使用方式

```java
    @Test
    public void geoTest() throws ParseException {
        /**
         * GeometryFactory工厂，参数一：数据精度 参数二空间参考系SRID
         */
        GeometryFactory geometryFactory = new GeometryFactory(new PrecisionModel(PrecisionModel.FLOATING), 4326);

        /**
         * 熟知文本WKT阅读器，可以将WKT文本转换为Geometry对象
         */
        WKTReader wktReader = new WKTReader(geometryFactory);

        /**
         * Geometry对象，包含Point、LineString、Polygon等子类
         */
        Geometry geometry = wktReader.read("POINT (113.53896635 22.36429837)");

        /**
         * 将二进制流的形式读取Geometry对象
         */
        WKBReader wkbReader = new WKBReader(geometryFactory);

        /**
         * 单纯的一个坐标点，单点可以创建Point，多点可以创建LineString、Polygon等
         */
        Coordinate coordinate = new Coordinate(1.00, 2.00);
        Point point = geometryFactory.createPoint(coordinate);

        Polygon polygon = geometryFactory.createPolygon(new Coordinate[]{
                new Coordinate(1, 2),
                new Coordinate(1, 2),
                new Coordinate(1, 2),
                new Coordinate(1, 2),
                new Coordinate(1, 2),
        });
        Geometry geometry1 = point;
        Geometry geometry2 = polygon;

        /**
         * WKT输出器，将Geometry对象写出为WKT文本
         */
        WKTWriter wktWriter = new WKTWriter();
        String write = wktWriter.write(point);
    }
```

#### 4.3 JTS中Geometry数据类型的子类

![img](https://upload-images.jianshu.io/upload_images/10533664-cec5c278f4d0240d.png?imageMogr2/auto-orient/strip|imageView2/2/w/1173/format/webp)



### 5. 使用JAVA向空间数据库新增数据

根据上面测试类中Api的使用，让我们总结几个要点

- 工厂类对象只需初始化一次，应放在配置类注入到Spring容器中
- 由前端或Excel导入相关坐标数据，生成Geometry对象
- 持久化Geometry对象到SqlServer

本例中推荐两种方式进行Geometry对象的持久化：

1. 获取Geometry对象的WKT文本，再使用SqlServer提供的`geometry :: STGeomFromText ()`函数将WKT文本存储为数据库Geometry类型
2. 将jts包中Geometry对象转换成SqlServer JDBC包中的Geometry对象，将Geometry对象以二进制的形式持久化到数据库

环境：
 本例代码基于JTS、SpringBoot、Mybatis-Plus、mssql-jdbc环境

### 6. 使用`TypeHandler`映射自定义对象字段插入Geometry数据

#### 6.1 自定义TypeHandler

当我们使用Mybatis框架时，Mybatis提供了自定义类型转换器TypeHandler实现特殊对象与Sql字段的映射关系

```java
import org.apache.ibatis.type.BaseTypeHandler;
import org.apache.ibatis.type.JdbcType;
import org.apache.ibatis.type.MappedTypes;
import org.locationtech.jts.geom.Geometry;
import org.locationtech.jts.io.WKTReader;

import java.sql.CallableStatement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

/**
 * @author wangqichang
 * @since 2019/8/28
 */
@Slf4j
@MappedTypes(value = {Geometry.class})
public class GeometryTypeHandler extends BaseTypeHandler<Geometry> {

    @Override
    public void setNonNullParameter(PreparedStatement preparedStatement, int i, Geometry geometry, JdbcType jdbcType) throws SQLException {
        /**
         * 获取jts包对象的wkt文本，再转换成sqlserver的Geometry对象
         * 调用ps的setBytes（）方法，以二进制持久化该geometry对象
         */
        com.microsoft.sqlserver.jdbc.Geometry geo = com.microsoft.sqlserver.jdbc.Geometry.STGeomFromText(geometry.toText(), geometry.getSRID());
        preparedStatement.setBytes(i, geo.STAsBinary());
    }

    @Override
    public Geometry getNullableResult(ResultSet resultSet, String s) {
        try {
            /**
             * 从ResultSet中读取二进制转换为SqlServer的Geometry对象
             * 使用jts的WKTReader将wkt文本转成jts的Geometryd对象
             */
            com.microsoft.sqlserver.jdbc.Geometry geometry1 = com.microsoft.sqlserver.jdbc.Geometry.STGeomFromWKB(resultSet.getBytes(s));
            String s1 = geometry1.toString();
            WKTReader wktReader = SpringContextUtil.getBean(WKTReader.class);
            Geometry read = wktReader.read(s1);
            return read;
        } catch (Exception e) {
            log.error(e.getMessage());
            throw new ServiceException(e.getMessage());
        }
    }

    @Override
    public Geometry getNullableResult(ResultSet resultSet, int i) throws SQLException {
        return null;
    }

    @Override
    public Geometry getNullableResult(CallableStatement callableStatement, int i) throws SQLException {
        return null;
    }
}
```

#### 6.2 实体对象

实体对象如下：

-  `objectid`为Integer类型，非自增（此字段为Arcgis维护，不能修改）`@TableId`是mybatis-plus插件的注解，告知插件该字段为主键字段，字段名为OBJECT，主键策略为用户输入
-  `shape`为jts的Geometry对象（该对象JSON序列化结果非常吓人，所以使用`@JsonIgnore`修饰）
-  `@KeySequence`也是mybatis-plus的插件，作用是标识该对象需要使用的主键序列名。此处我实现了一个`IKeyGenerator`，作用类似于插入数据前查询Oracle的序列名以填充主键。

```java
@Data
@TableName("LINE_WELL")
@KeySequence(value = "LINE_WELL",clazz = Integer.class)
public class Well extends MyGeometry implements Serializable {

    @TableId(value = "OBJECTID", type = IdType.INPUT)
    private Integer objectid;

    @JsonIgnore
    protected Geometry shape;
}
```

#### 6.3 自定义主键生成策略

在arcgis中，空间表中的主键字段为int，并且非自增，不能进行修改。当修改为自增时arcgis会出现一些错误。因此，java后台插入空间数据需要自己完成主键的查询生成。
 `IKeyGenerator`是Mybatis-Plus提供的接口。此实现的作用是，当指定这个主键生成策略时，mp框架将会在新增数据前调用此实现，将结果赋值给对象的ID（类似于Oracle的序列）
 注意，该类需要注入到Spring容器中

```java
import com.baomidou.mybatisplus.core.incrementer.IKeyGenerator;

/**
 * @author wangqichang
 * @since 2019/8/30
 */
public class SqlServerKeyGenerator implements IKeyGenerator {
    @Override
    public String executeSql(String incrementerName) {
        return "select max(OBJECTID)+1 from " + incrementerName;
    }
}
```

#### 6.4 Geometry对象持久化

当我们调用mybatis-plus提供的方法持久化对象

```java
 String str = "POLYGON ((113.52048666400003 22.248443089000034, 113.5206744190001 22.24822462700007, 113.52082998700007 22.248343788000057, 113.52060468200011 22.248547355000028, 113.52048666400003 22.248443089000034))";
        Geometry read = null;
        try {
            /**
             * 这里使用wkt文本生成了一个jts包下的Geometry对象
             */
            read = SpringContextUtil.getBean(WKTReader.class).read(str);
        } catch (ParseException e) {
            e.printStackTrace();
        }
        Well well = new Well();
        well.setShape(read);
        //这里是Mybatis-Plus提供的save接口，调用其内部实现直接储存对象
        wellService.save(well);
        System.out.println("持久化成功");
```

执行日志如下：
数据插入前执行了`SqlServerKeyGenerator`中的sql获取主键
插入代码中字段shape为Geometry对象的二进制

```java
2019-08-30 15:54:23.541  INFO 8484 --- [nio-8905-exec-1] jdbc.sqltiming                           : SELECT max(OBJECTID) + 1 FROM LINE_WELL 
 {executed in 4 msec}
2019-08-30 15:54:23.631  INFO 8484 --- [nio-8905-exec-1] jdbc.sqltiming                           : INSERT INTO LINE_WELL (OBJECTID, shape) VALUES (3, '<byte[]>') 
 {executed in 17 msec}
```

### 7. 手写xml插入Geometry数据

使用SqlServer提供的函数`geometry :: STGeomFromText( #{wktText},4326)`将Geometry转换成WKT文本再进行插入

```xml
<insert id="insertCorridorBySql" parameterType="com.zh.xxx.entity.xxx" useGeneratedKeys="true"
            keyProperty="objectid">
        INSERT INTO [LINE_CORRIDOR] (
         shape
        )
        values (
        geometry :: STGeomFromText( #{wktText},4326)
        )
   </insert>
```

注意,wktText是一个非表字段的临时字段，我在此定义了一个父类，所有包含Geometry的空间表实体均继承此类，用于处理wkt文本

```java
import org.locationtech.jts.geom.Geometry;
import org.locationtech.jts.io.WKTWriter;

import java.io.Serializable;

/**
 * 针对Geometry获取Wkt文本字段做处理的Geometry父类，getWktText替代getText,输出三维wkt文本
 * 针对sql_server无法识别POLYGON Z 语法，对wkt文本进行替换
 */
@Data
public class MyGeometry implements Serializable {

    /**
     * 三维wkt输出，默认为2D不带Z
     */
    @TableField(exist = false)
    @JsonIgnore
    private WKTWriter wktWriter = new WKTWriter(3);

    /**
     * sql_server 与 jts wkt不兼容问题
     */
    @TableField(exist = false)
    @JsonIgnore
    private static final String THREE_D_PRIFIX = "POLYGON Z";
    @TableField(exist = false)
    @JsonIgnore
    private static final String TWO_D_PRIFIX = "POLYGON";

    @JsonIgnore
    protected Geometry shape;


    @TableField(exist = false)
    @JsonIgnore
    private String wktText;

    public String getWktText() {
        if (StrUtil.isBlank(wktText)){
            if (getShape() != null) {
                String wkt = wktWriter.write(shape);
                if (wkt.startsWith(THREE_D_PRIFIX)) {
                    wktText = StrUtil.replace(wkt, THREE_D_PRIFIX, TWO_D_PRIFIX);
                } else {
                    wktText = wkt;
                }
            }
        }
        return wktText;
    }
}
```



# WebGIS五剑客

## 1 数据生产

1.1 uDig

uDig（[http://udig.refractions.net/](https://link.zhihu.com/?target=http%3A//udig.refractions.net/)）是一个基于Java开源的桌面应用框架，它构建在Eclipse RCP和GeoTools（一个开源的Java GIS包)上。开发者可通过这个平台来创建新的在uDig 基础上衍生的应用程序，uDig 是WebGIS 的一个核心组件。Geotools 是一个开源的Java GIS 工具包，可利用它来开发符合标准的地理信息系统。Geotools 提供了OGC( Open Geospatial Consortium) 规范的一个实现来作为他们的开发。它是一个开源的空间数据查看器和编辑器，它尤其扩展加强了网络地图服务的功能。目前最新版本更新到2.0。

uDig支持WMS,WFS，WPS等GIS服务，同时提供了丰富的地图数据编辑功能，包括点、线、面和图片形式的编辑。但实际上应用最多的就是使用uDig编辑shp格式的地图数据，从中提取作用域地图发布的样式文件（*。sld）。通常使用默认文字编码的地图数据通过WMS服务显示处的地图中文标签是乱码。用uDig导出shp格式地图数据的SLD样式文件，可以有效的解决中文标签乱码问题。

1.2 QGIS

QGIS（[https://www.qgis.org/zh_CN/site/](https://link.zhihu.com/?target=https%3A//www.qgis.org/zh_CN/site/)）全称Quantum GIS，基于Qt，使用C++开发的开源版桌面地理信息系统。该系统可运行在Linux、Unix、Mac OSX和Window等平台之上。QGIS最大的特点是它把图层当做数据处理的最基本单位，实际上它支持的图层具有很广泛的来源，主要有矢量文件图层、CAD图层、影像数据图层和WMS图层。QGIS 支持vector， raster 和database 格。它能够很好地支持PostGIS。目前最新版本更新到3.10.2。

1.3 QGIS和uDig的比较

![img](https://pic4.zhimg.com/80/v2-13e99ab7bfcb30efb9c0d410a9738e5f_720w.jpg)

## 2 数据存储

**普通数据库 VS 空间数据库：空间数据库像存储和操作数据库中其他任何对象一样去存储和操作空间对象。**

先看看空间数据库由来：

- 在传统的第一代**地理信息系统**（GIS）实现中，所有的**空间数据**都存储在**平面文件**中，需要专门的GIS软件来解释和操作这些数据。 这些第一代管理系统旨在满足用户的需求，其中所有所需的数据都在用户的组织领域中。它们是专为处理**空间数据**而构建的专有的、独立的系统。
- 第二代空间系统将一些数据存储在**关系数据库（RDBMS）**中（通常是“**属性**”或**非空间部分**），但仍然缺乏直接集成所具有的灵活性。
-  **真正的空间数据库诞生于人们开始把空间特征当作第一级数据库对象的时候**。

​    **空间数据库**将**空间数据**和**对象关系数据库**（Object Relational database）完全集成在一起。实现从以GIS为中心向**以数据库为中心**的转变。

![空间数据存储的体系架构的发展](source/img/images/入坑GIS/20181221155257185.png)

>  从上图可以看出，有了空间数据库之后，就不再需要专门的GIS数据引擎（GIS Data Engine）去处理和操纵空间数据了，应用程序只需要通过SQL语言就能轻松地操纵空间数据。

2.1 **PostgreSQL**

PostgreSQL（**对象-关系数据库管理系统 **【ORDBMS】）是由加州大学伯克利分校计算机系开发的Postgres软件包发展而来的。PostgreSQL几乎支持所有SQL构件（包括子查询，事物和用户定义类型和函数），并且可以获得非常广阔范围的开发语言绑定（包括C，C++，Java,Perl,tcl,和Python）。在空间数据管理方面, PostgreSQL定义了一系列的几何数据类型, 包括点(point),线(line),线段(lseg), 方形(box), 闭合和开放路径(path),多边形(polygon), 圆(circle)。但是PostgreSQL提供的几何类型并不支持OpenGIS的SFS规范, 缺乏复杂几何类型, 没有提供空间分析和投影变换模块, 很难达到GIS的应用要求。

2.2 **PostGIS**

PostGIS通过向PostgreSQL添加对**空间数据类型**、**空间索引**和**空间函数**的支持，将PostgreSQL数据库管理系统转换为**空间数据库**。

PostGIS（[http://www.postgis.org/](https://link.zhihu.com/?target=http%3A//www.postgis.org/)）是一个功能强大的**开源空间数据库**(Oracle Spatial和SQL Server(2008和之后版本）也是属于空间数据库，不开源)。它是在1986年诞生于加州大学伯克利分校。PostGIS是PostgreSQL（对象-关系型数据库管理系统）的一个扩展。它支持所有的空间数据类型与一系列重要的GIS 函数，包括完全的OpenGIS 支持、拓扑结构和用于查看、编辑GIS 数据桌面用户相关工具和基础网络访问工具。作为PostgreSQL对象关系数据库系统的扩展模块，

- PostGIS 支持GIS 空间数据的存储，PostGIS 遵循**OGC**(Open GIS Consortium **开放地理空间联盟**) 的Simple Feature for SQL。

- PostGIS在PostgreSQL基础上增加了存储空间数据的能力，与Oracle中Spatial相似。PostGIS也具有大型数据库的特性，如数据备份，数据库恢复，灾难恢复等。

- PostGIS支持所有的空间数据类型，这些类型包括：点(POINT)、线(LINESTRING)、 多边形(POLYGON)、多点(MULTIPOINT)、 多线(MULTILINESTRING)、 多多边形(MULTIPOLYGON)和集合对象(GEOMETRYCOLLECTION)等。

- PostGIS支持所有的**对象表达方法**，比如**WKT**和**WKB**。PostGIS支持所有的数据存取和构造方法，如GeomFromText()、AsBinary()，以及GeometryN()等。

  > <u>OpenGIS的规范(OGC制定的)</u>中定义了两种表述空间对象的标准方式(又称：**SFS** 【简单服务标准】(Simple Features Interface Standard))：
  >
  > - 一个是**WKT**（the Well-Known Text）形式；
  >
  > - 另一个是**WKB**（the Well-Known Binary）形式，他解决了WKT表达方式冗余的问题，便于传输和在数据库中存储相同的信息；
  >
  > -  WKT与WKB在GIS中的重要作用在于，**它们能利用文本简洁明了的表达矢量空间要素的几何信息，使得几何信息能以字段的形式存储于数据库中。**
  >
  >   
  >
  >   除了上面两种，还有一种<u>不属于OGC制定的对象表达方法</u>：**GeoJSON** 一种JSON格式的Feature信息输出格式，它便于被JavaScript等脚本语言处理，OpenLayers等地理库便是采用GeoJSON格式。此外，TopoJSON等更精简的扩展格式。
  >
  >   目前PostGIS中无论是WKT还是WKB，支持的矢量数据类型都为以下7种：

  > ```mysql
  >  POINT(0 0)
  >  LINESTRING(0 0,1 1,1 2)
  >  POLYGON((0 0,4 0,4 4,0 4,0 0),(1 1, 2 1, 2 2, 1 2,1 1))
  >  MULTIPOINT((0 0),(1 2))
  >  MULTILINESTRING((0 0,1 1,1 2),(2 3,3 2,5 4))
  >  MULTIPOLYGON(((0 0,4 0,4 4,0 4,0 0),(1 1,2 1,2 2,1 2,1 1)), ((-1    -1,-1 -2,-2 -2,-2 -1,-1 -1)))
  >   GEOMETRYCOLLECTION(POINT(2 3),LINESTRING(2 3,3 4))
  > ```

- PostGIS提供简单的空间分析函数(如Area和Length)同时也提供其他一些具有复杂分析功能的函数，比如Distance。

- PostGIS提供了对于元数据的支持，如GEOMETRYCOLUMNS和SPATIAL REF SYS，同时，PostGIS也提供了相应的支持函数，如AddGeometryColumn和DropGeometryColumn。

- PostGIS提供了一系列的二元谓词(如Contains、Within、Overlaps和Touches)用于检测空间对象之间的空间关系，同时返回布尔值来表征对象之间符合这个关系。

- PostGIS提供了空间操作符(如Union和Difference)用于空间数据操作。比如，Union操作符融合多边形之间的边界。两个交迭的多边形通过Union运算就会形成一个新的多边形，这个新的多边形的边界为两个多边形中最大边界。

地理对象表达方式示例（WKT与GeoJSON对比）：   

| Type            | Shape                                                        | WKT                                                          | GeoJSON                                                      |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Point           | ![img](https://images2015.cnblogs.com/blog/756316/201601/756316-20160120105806000-70608107.png) | POINT (30 10)                                                | { "type": "Point", "coordinates": [30, 10] }                 |
| LineString      | ![img](https://images2015.cnblogs.com/blog/756316/201601/756316-20160120105816187-796816973.png) | LINESTRING (30 10, 10 30, 40 40)                             | { "type": "LineString", "coordinates": [ [30, 10], [10, 30], [40, 40] ] } |
| Polygon         | ![img](https://images2015.cnblogs.com/blog/756316/201601/756316-20160120105841750-584019839.png) | POLYGON ((30 10, 40 40, 20 40, 10 20, 30 10))                | { "type": "Polygon", "coordinates": [ [[30, 10], [40, 40], [20, 40], [10, 20], [30, 10]] ] } |
|                 | ![img](https://images2015.cnblogs.com/blog/756316/201601/756316-20160120105851531-1295980091.png) | POLYGON ((35 10, 45 45, 15 40, 10 20, 35 10), (20 30, 35 35, 30 20, 20 30)) | { "type": "Polygon", "coordinates": [ [[35, 10], [45, 45], [15, 40], [10, 20], [35, 10]], [[20, 30], [35, 35], [30, 20], [20, 30]] ] } |
| MultiPoint      | ![img](https://images2015.cnblogs.com/blog/756316/201601/756316-20160120105938078-1525053158.png) | MULTIPOINT ((10 40), (40 30), (20 20), (30 10))              | { "type": "MultiPoint", "coordinates": [ [10, 40], [40, 30], [20, 20], [30, 10] ] } |
| MultiLineString | ![img](https://images2015.cnblogs.com/blog/756316/201601/756316-20160120105944453-2096220500.png) | MULTILINESTRING ((10 10, 20 20, 10 40), (40 40, 30 30, 40 20, 30 10)) | { "type": "MultiLineString", "coordinates": [ [[10, 10], [20, 20], [10, 40]], [[40, 40], [30, 30], [40, 20], [30, 10]] ] } |
| MultiPolygon    | ![img](https://images2015.cnblogs.com/blog/756316/201601/756316-20160120105952187-1699089876.png) | MULTIPOLYGON (((30 20, 45 40, 10 40, 30 20)), ((15 5, 40 10, 10 20, 5 10, 15 5))) | { "type": "MultiPolygon", "coordinates": [ [ [[30, 20], [45, 40], [10, 40], [30, 20]] ], [ [[15, 5], [40, 10], [10, 20], [5, 10], [15, 5]] ] ] } |
|                 | ![img](https://images2015.cnblogs.com/blog/756316/201601/756316-20160120105958531-2054918008.png) | MULTIPOLYGON (((40 40, 20 45, 45 30, 40 40)), ((20 35, 10 30, 10 10, 30 5, 45 20, 20 35), (30 20, 20 15, 20 25, 30 20))) | { "type": "MultiPolygon", "coordinates": [ [ [[40, 40], [20, 45], [45, 30], [40, 40]] ], [ [[20, 35], [10, 30], [10, 10], [30, 5], [45, 20], [20, 35]], [[30, 20], [20, 15], [20, 25], [30, 20]] ] ] } |

**WKT与geojson的主要区别是wkt是单独用来表示空间点线面数据的，而geojson还可以用来表示空间数据和属性数据的集合，**下面是shp面数据转geojson，其中还包含图层信息等，而wkt并不能表示这个。

```properties
"type": "FeatureCollection",
"name": "a",
"crs": { "type": "name", "properties": { "name": "urn:ogc:def:crs:EPSG::3857" } },
"features": [
{ "type": "Feature", "properties": { "AREA": 0.0, "PERIMETER": 0.016, "BOU2_4M_": 914, "BOU2_4M_ID": 3089, "ADCODE93": 810000, "ADCODE99": 810000, "NAME": "棣欐腐鐗瑰埆琛屾斂鍖" }, "geometry": { "type": "Polygon", "coordinates": [ [ [ 12720007.326881121844053, 2544771.959127825684845 ], [ 12720092.256912549957633, 2545226.735669989138842 ], [ 12720510.112667175009847, 2545228.571321710944176 ], [ 12720666.383925, 2544938.081929029431194 ], [ 12720007.326881121844053, 2544771.959127825684845 ] ] ] } },
{ "type": "Feature", "properties": { "AREA": 0.0, "PERIMETER": 0.013, "BOU2_4M_": 915, "BOU2_4M_ID": 3090, "ADCODE93": 810000, "ADCODE99": 810000, "NAME": "棣欐腐鐗瑰埆琛屾斂鍖" }, "geometry": { "type": "Polygon", "coordinates": [ [ [ 12730249.039371021091938, 2544414.938326342497021 ], [ 12730198.930652478709817, 2544711.384370831772685 ], [ 12730315.28479553386569, 2544832.534102902282029 ], [ 12730576.019992018118501, 2544807.065052719321102 ], [ 12730715.30524355918169, 2544607.902998786885291 ], [ 12730353.503309676423669, 2544404.842732572928071 ], [ 12730249.039371021091938, 2544414.938326342497021 ] ] ] } }
]
}
```



2.3 PostGIS与PostgreSQL的对比

![img](https://pic3.zhimg.com/v2-cb8bab34747c4fa151ff8b7610115ad6_r.jpg)

>  总的来说PostGIS相当于PostgreSQL的升级版，在此基础上完善了空间分析操作以及更复杂的几何量算。

2.4 MySQL

MySQL（[https://www.mysql.com/](https://link.zhihu.com/?target=https%3A//www.mysql.com/)）是一种小型关联数据库管理系统。这种数据库的最大特点是，它将数据保存在不同的数据表中，而不是将全部的数据都存放在仓库中，这样提高了数据库的灵活性，增加了查询的速度。它现在同样支持空间扩展，该数据库能够生成、保存及分析空间特征，它使用了OGC建议的含有Geometry类型的，负荷SQL语句环境的一个自己，提供了一组对空间数据实现很多操作的函数，这些函数可以在各种格式间转换几何值，并且访问几何值的定性或定量等属性信息。开发者为瑞典MySQLAB公司。该公司在2008年被Sun公司收购。

MySQL是完全网络化的跨平台关系型数据库，因为其体积小、速度快、总体拥有成本低，且开放源码，被广泛的应用于Internet上的中小型网站中。

2.5 MongoDB

MongoDB（[https://www.mongodb.com/](https://link.zhihu.com/?target=https%3A//www.mongodb.com/)）是一个开源面向文档的NoSQL 数据库，用C++编写。它提供一种强大、灵活、可扩展的数据存储形式。MongoDB功能丰富，内置MapＲeduce聚合的支持，支持地理空间索引。MongoDb具有弱一致性，保证用户的访问速度，文档结构的访问方式，能够更便捷地获取数据，内置GirdFS 支持大容量的储存，内置Sharding，第三方支持丰富，性能优越。MongoDB在存储空间数据比传统的关系数据库如MySQL 和PostgreSQL 更具优势。MongoDB 对空间数据是原生支持，不需要安装第三方空间数据库引擎即可构建空间索引进行进行快速的空间查询。MongoDB 支持平面( 2d) 和球面( 2dsphere indexes) 两种空间索引。面向大众型的GIS 应用会存储大量的POI 数据或者用户产生的位置数据，利用MongoDB 储存可以提高检索效率。MongoDB 支持MapＲeduce计算模型，处理海量LBS数据有较高的效率。MongoDB有多种基于的Node.JS的第三方库实现对MongoDB的操作，这样使得对数据的操作更加的便捷。

2.6 PostGIS、MySQL和MongoDB的比较

![img](https://pic1.zhimg.com/v2-2c5834b1a18a86c1540dc0e654232bc0_r.jpg)

总的来说，PostGIS对GIS原生支持的最多；MySQL胜在速度快，体积小但并不是所有功能都免费；MongoDB处理数据快，适用于大数据级别的数据处理，在弱GIS应用中或简单的LBS应用中有天然的优势。

## 3 GIS服务

3.1 GeoServer

GeoServer([http://geoserver.org/](https://link.zhihu.com/?target=http%3A//geoserver.org/))是一个遵守OGC开放标准的开源地图服务器，它支持J2EE规范，且实现了WCS、WMS（网络地图服务）及WFS（网络要素服务）规格，支持TransactionWFS(WFS-T)，其技术核心是整合了颇负盛名的JavaGISolkit--GeoTools。对于空间信息存储，它支持ESRI Shapefile及PostGIS、Oracle、ArcSDE等空间数据库，输出的GML档案满足GML2.1的要求。由于它是纯Java的，所以更适合于复杂的环境要求，而且由于它的开源，所以开发组织可以基于GeoServer灵活实现特定的目标要求，而这些都是商业GIS组件所缺乏的。GeoServer作为一个纯粹的Java实现，被部署在应用服务器中，简单的如Tomcat等；它的WMS和WFS组件响应来自于浏览器或uDig的请求，访问配置的空间数据库，如PostGIS、OracleSpatial等，产生地图和GML文档传输至客户端。

具有以下优点：

1） 用 java 语言编写、标准的 J2EE 框架、基于 servlet和STRUTS 框架、 支持高效的 Spring 框架开发；

2） 兼容 WMS 和 WFS 特性、支持 WFS-T 规范；

3） 高效的数据库支持 PostGIS、ShapeFile、ArcSDE,Oracle、MySQL 等；

4） 支持上百种投影；

5） 能够将网络地图输出为 jpeg、gif、png 、SVG、GML、KML等格式；

6）能够运行在任何基于J2EE/servle框架之上；

7）嵌入MapBuilder支持AJAX的地图客户端

8）实现了在线编辑空间数据、生成专题地图；

9）地图发布是用XML文件。

10）支持Goodgle Maps;

11)可发布KML数据，可与GoogleEarth影像叠加。

3.2 MapServer

MapServer（[https://mapserver.org/](https://link.zhihu.com/?target=https%3A//mapserver.org/)） 源自美国太空总署（NASA）资助的美国明尼苏达大学(University of Minnesota)ForNet项目，最终衍生为一个WebGIS开源软件，是一个功能强大的跨平台的网络地图服务软件包, 可以应用于UNIX/Linux, Windows, MacOSX, Solaris等平台，支持的语言包括 Python，PHP，Perl，Java，Tcl，C# 等。MapServer使用几个知名的开放源代码软件完成数据格式转换、地图投影转换、空间数据库的大数据量处理等, 而本身专注于地图绘制、地图图形格式、接口环境、兼容OGC互操作规范等方面。

MapServer具有强大的空间数据的网络发布功能，支持多种数据格式，使得在WebGIS 中整合空间数据和非空间数据变得更加容易。MapServer是基于胖服务器/瘦客户端模式开发的 WebGIS 平台。主要是因为处理空间数据的任务主要在服务器端完成，在通过客户端发送请求的时候，服务器依据客户端请求，执行相应的操作并返回数据，客户端将数据处理显示返回用户。它的核心模块主要是通过C语言编写完成，提供了两种开发模式，一种是基于CGI的，另一种是MapScript方式； 在服务器端可以使用任一模块，编写WebGIS程序。它遵守OGC制定的WMS、WFS、WCS和GML等一系列规范，支持分布式访问和互操作。MapServer作为WebGIS解决方案是基于对象的，基本配置文件 MapFile 和 MapScript 模块的API组织都是基于对象的。

3.3 MapProxy

MapProxy（[https://mapproxy.org/](https://link.zhihu.com/?target=https%3A//mapproxy.org/)）是 WMS 网页地图服务与切片服务提供者的瑞士军刀。它对已有地图服务的数据服务进行缓存、加速和转换，服务任何支持 OGC 标准的桌面与网页客户端。 MapProxy 开发与部署方式灵活，易于同 Apache/Nginx 环境等集成。MapProxy 也能作为一个独立服务器使用，这是对于新用户而言最容易的方式。 　MapProxy 的默认示例是一个基本的 WMS 切片客户端，它显示了配置的各个图层；不具备改变投影等复杂功能，但足以验证服务是否正常运行。

3.4 MapServer和GeoServer的总体对比

先看历史悠久产品成熟的MapServer。Mapserver在国内用户广泛，其内核使用C++编写，基于CGI脚本实现，页面调用支持PHP、JSP等多种语言，并且对OGC的WMS和WFS规范提供支持。Map文件是Mapserver工程中关键的结构配置文件，定义了地图范围、数据源、地图图层、投影方式以及符号标记等内容。如果在Linux平台下快速配置MapServer地图服务器，使用Maptools发布的 FGS（FOSS GIS Suite）套件无疑是最佳方案，套件集成了整套的MapServer、Apache、PHPMapScript以及GDAL库等众多内容，并且提供安装后就可直接浏览的地图DEMO。

再看GeoServer，基于Java和Geotools库开发的Geoserver功能全面且遵循OGC开放标准，并由同时拥有PostGIS与uDig的Refractions公司提供支持。GeoServer对发布WFS-T和WMS服务提供便捷的支持，并以XML文件描述所有地图服务，同时即将加入的FROGS（Framework for Open Geospatial Services）项目将着重提供对WPS规范的支持。GeoServer的开发组很活跃，邮件咨询的问题很快会得到Jody Garnett和组内其他成员的回复。

比较来说，Mapserver对WMS的支持更为高效，而Geoserver则更擅长于结合WFS规范的属性查询。当然，对于使用者来说，最主要的区别还是传统CGI脚本与J2EE在技术上的分歧。但就发展趋势来看，J2EE的架构明显要占据优势，并且Geoserver拥有基于Eclipse RCP平台的开源客户端uDig。随着Geoserver的版本更新，相信其有实力成为今后开源WebGIS解方案的主流选择。

总的来说:

- **功能上：MapServer弱于GeoServer。**
- **效率上：Mapserver对WMS（Web Map service）的支持更为高效，而Geoserver则更擅长于结合WFS（Web Feature service）规范的属性查询。**

## 4 Web服务

4.1 Apache

Apache（[https://www.apache.org/](https://link.zhihu.com/?target=https%3A//www.apache.org/)）是目前世界使用排名第一的Web服务器。根据Web服务器调查公司Netcrafe调查, 全Internet有50%以上的Web服务器都在使用Apache。1995 年4月, 最早的Apache(0.6.2 版)由ApacheGroup公布发行。Apache Group是一个完全通过Internet进行运作的非盈利机构,由它来决定ApacheWeb服务器的标准发行版中应该包含哪些内容。准许任何人修改隐错, 提供新的特征和将它移植到新的平台上, 以及其他的工作。Apache的特点是简单、速度快、性能稳定, 可以运行在几乎所有广泛使用的计算机平台上, 并可做代理服务器来使用。Apache是Linux主流的服务器, 为Linux的迅速繁荣立下了汗马功劳。

4.2 Node.js

Node.js（[https://nodejs.org/](https://link.zhihu.com/?target=https%3A//nodejs.org/)） 是一个让JavaScript 运行在服务端的开发平台，Node.js基于Chrome的V8引擎，V8引擎是Google公司使用C++开发的高性能JavaScript引擎，可以在多种环境下使用。使用V8引擎的Node.js让作为前端开发语言的Javascript能够自由运行在服务器环境下。Node.js借助事件驱动，非阻塞I/O 模型变得轻量和高效，非常适合运行在分布式设备的数据密集的实时应用。Node.js 自2009年出现一直备受关注，开发社区庞大、数以万计的第三方模块为构建网络程序提供了很大便捷。

4.3 Tomcat

Tomcat（[http://tomcat.apache.org/](https://link.zhihu.com/?target=http%3A//tomcat.apache.org/)） 是Apache 软件基金会( Apache Software Foundation)的Jakarta 项目中的一个核心项目，由Apache，Sun和其他一些公司及个人一块开发而成。由于有了Sun 的参与与支持，最新的Servlet 与JSP 规范总是能在Tomcat中得到完美体现，Tomcat 5 支持最新的Servlet 2.4 与JSP2．0 规范。因为Tomcat 技术先进、性能稳定，而且免费，因而深受Java 爱好者的青睐并得到了部分软件开发商的认可，成为目前比较流行的Web 应用服务器。Tomcat 是一个轻量级的服务器，运行时占用的系统资源少，安装和部署方便，在中小型系统和并发访问用户不是很多的场合下被普遍使用，是开发和调试JSP 程序的首选。

## 5 前端渲染

5.1 OpenLayers

OpenLayers([http://openlayers.org/](https://link.zhihu.com/?target=http%3A//openlayers.org/))是一个开源的jS框架，用于在您的浏览器中实现地图浏览的效果和基本的zoom，pan等功能。OpenLayers支持的地图来源包括了WMS，GoogleMap，KaMap，MSVirtualEarth等等，您也可以用简单的图片作为源，在这一方面OpenLayers提供了非常多的选择。此外，OpenLayers实现了行业标准的地理数据访问方法如OGC的Web Mapping Service(WMS)and Web Feature Service(WFS)协议。OpenLayers可以简单的在任何页面中放入动态的地图。它可以从多种的数据源加载显示地图。MetaCarta公司开始开发了OpenLayers的初始版本同时将它开放给了公众以作为以后各种地理信息系统的应用。

5.2 OpenScales

OpenScales是一个基于Flex的优秀前台地图框架，可以用来开发各种网络版、手机版和桌面版地图程序。OpenScales是开源的、免费的客户端开发框架，基于LGPL开源协议，它在FlashPlayer中运行，可以在各个浏览器中使用，具有很好的跨平台特性。鉴于主要对Flash的友好支持，故可不放在第一梯队考虑

5.3 Leaflet.JS

Leaflet([https://leafletjs.com/](https://link.zhihu.com/?target=https%3A//leafletjs.com/)) 是一个为构建交互性好、适用于移动设备地图应用，而开发的现代的、开源的 JavaScript 库。 它是由 Vladimir Agafonkin 带领一个专业贡献者团队开发，代码量很小(仅33Kb)，但具有开发人员开发在线地图的大部分功能。 Leaflet设计坚持简便、高性能和可用性好的哲学思想，在所有主要桌面和移动平台能高效运作， 在现代浏览器上会利用HTML5和CSS3的优势，同时也支持旧的浏览器访问。它支持插件扩展，有一个友好、易于使用的API文档和一个简单的、可读的源代码。

Leaflet 强大的开源库插件涉及地图应用的各个方面包括地图服务、数据提供、数据格式、地理编码、路线和路线搜索、地图控件和交互等类型的插件共有140多个。这些控件大大扩展了LeafLet 的功能，同时也可以设计开发自定义的控件，具有良好的可扩展性。其中Esri提供面向ArcGIS Services的leaflet的插件，插件支持Esri底图服务、要素服务、瓦片和动态图服务。Leaflet API可以调用各种在线的地图服务天地图、Google 地图、高德地图可以满足多种服务的需要。系统外观UI采用BootStrap 响应式结构设计框架对各种尺寸的屏幕进行优化显示，开发者只需具有基本的CSS基础就可以设计适合多终端、多平台的GIS应用。

5.4 Cesium

Cesium（[https://cesium.com/cesiumjs/](https://link.zhihu.com/?target=https%3A//cesium.com/cesiumjs/)）基于 WebGL 的前端3D JavaScript 类库。 Cesium在2016年3月份左右推出3D Tiles数据规范，在gltf基础上提供了LOD能力， 就是Web环境下海量三维模型数据。

# postGIS使用

### 空间数据类型

PostGIS还有一种额外的空间数据类型**Geography（地理对象）**，跟Geometry的区别是Geography的空间运算都是基于EPSG:4326大地坐标系下的椭球面运算（Geometry的运算使用的都是笛卡尔平面直角坐标系），非常实用但会有额外开销，个人推荐存储时用Geometry，做距离面积等测量运算的时候在转换为Geography。

### SRID是什么？(空间引用标识符)

每个空间实例都有一个空间引用标识符(SRID)。SRID 对应于基于特定椭圆体的空间引用系统，可用于平面球体映射或圆球映射。空间列可包含具有不同SRID的对象。然而，在使用SQL Server空间数据方法对数据执行操作时，仅可使用具有相同SRID的空间实例。从两个空间数据实例派生的任何空间方法的结果仅在这两个实例具有相同的SRID (该SRID基于相同的用于确定实例坐标的度量单位、数据和投影)时才有效。SRID 最常见的度量单位为米或平方米。

### WKT定义几何对象格式：

POINT(0 0) ——点
LINESTRING(0 0,1 1,1 2) ——线
POLYGON((0 0,4 0,4 4,0 4,0 0),(1 1, 2 1, 2 2, 1 2,1 1)) ——面
MULTIPOINT(0 0,1 2) ——多点
MULTILINESTRING((0 0,1 1,1 2),(2 3,3 2,5 4)) ——多线
MULTIPOLYGON(((0 0,4 0,4 4,0 4,0 0),(1 1,2 1,2 2,1 2,1 1)), ((-1 -1,-1 -2,-2 -2,-2 -1,-1 -1))) ——多面
GEOMETRYCOLLECTION(POINT(2 3),LINESTRING((2 3,3 4))) ——几何集合

## 常用函数：

**geometry转geojson：st_asgeojson（geometry）**

**geojson转geometry：st_geomfromgeojson(geojson)**

**wkt转geometry** st_geomfromtext(wkt,wkid)
**geometry转wkt** st_astext(geom)
获取点对象x、y坐标值 st_x(geom)、st_y(geom)
获取线/面对象四至 st_xmin(geom)、st_ymin(geom)、st_xmax(geom)、st_ymax(geom)
计算两点之间距离 st_distance(geom,geom) / st_distance(wkt,wkt)
计算线的长度 st_length(geom) / st_length(wkt)
计算面积 st_area(geom) / st_area(wkt)
缓冲区计算 st_buffer(geom,distance) / st_buffer(wkt,distance)

### 管理函数：

添加几何字段 AddGeometryColumn(, , , , , )
删除几何字段 DropGeometryColumn(, , )
检查数据库几何字段并在geometry_columns中归档 Probe_Geometry_Columns()
给几何对象设置空间参考（在通过一个范围做空间查询时常用） ST_SetSRID(geometry, integer)

### 几何对象关系函数 ：

获取两个几何对象间的距离 ST_Distance(geometry, geometry)
如果两个几何对象间距离在给定值范围内，则返回TRUE ST_DWithin(geometry, geometry, float)
判断两个几何对象是否相等
（比如LINESTRING(0 0, 2 2)和LINESTRING(0 0, 1 1, 2 2)是相同的几何对象） ST_Equals(geometry, geometry)
判断两个几何对象是否分离 ST_Disjoint(geometry, geometry)
**判断两个几何对象是否相交** ST_Intersects(geometry, geometry)
判断两个几何对象的边缘是否接触 ST_Touches(geometry, geometry)
判断两个几何对象是否互相穿过 ST_Crosses(geometry, geometry)
**判断A是否被B包含** ST_Within(geometry A, geometry B)
判断两个几何对象是否是重叠 ST_Overlaps(geometry, geometry)
**判断A是否包含B** ST_Contains(geometry A, geometry B)
判断A是否覆盖 B ST_Covers(geometry A, geometry B)
判断A是否被B所覆盖 ST_CoveredBy(geometry A, geometry B)
通过DE-9IM 矩阵判断两个几何对象的关系是否成立 ST_Relate(geometry, geometry, intersectionPatternMatrix)
获得两个几何对象的关系（DE-9IM矩阵） ST_Relate(geometry, geometry)

### 几何对象处理函数：

获取几何对象的中心 ST_Centroid(geometry)
面积量测 ST_Area(geometry)
长度量测 ST_Length(geometry)
返回曲面上的一个点 ST_PointOnSurface(geometry)
获取边界 ST_Boundary(geometry)
获取缓冲后的几何对象 ST_Buffer(geometry, double, [integer])
获取多几何对象的外接对象 ST_ConvexHull(geometry)
**获取两个几何对象相交的部分** ST_Intersection(geometry, geometry)
将经度小于0的值加360使所有经度值在0-360间 ST_Shift_Longitude(geometry)
获取两个几何对象不相交的部分（A、B可互换） ST_SymDifference(geometry A, geometry B)
从A去除和B相交的部分后返回 ST_Difference(geometry A, geometry B)
返回两个几何对象的合并结果 ST_Union(geometry, geometry)
返回一系列几何对象的合并结果 ST_Union(geometry set)
用较少的内存和较长的时间完成合并操作，结果和ST_Union相同 ST_MemUnion(geometry set)

### 几何对象存取函数：

**获取几何对象的WKT描述 ST_AsText(geometry)**
**获取几何对象的WKB描述 ST_AsBinary(geometry)**
获取几何对象的空间参考ID ST_SRID(geometry)
获取几何对象的维数 ST_Dimension(geometry)
获取几何对象的边界范围 ST_Envelope(geometry)
判断几何对象是否为空 ST_IsEmpty(geometry)
判断几何对象是否不包含特殊点（比如自相交） ST_IsSimple(geometry)
判断几何对象是否闭合 ST_IsClosed(geometry)
判断曲线是否闭合并且不包含特殊点 ST_IsRing(geometry)
获取多几何对象中的对象个数 ST_NumGeometries(geometry)
获取多几何对象中第N个对象 ST_GeometryN(geometry,int)
获取几何对象中的点个数 ST_NumPoints(geometry)
获取几何对象的第N个点 ST_PointN(geometry,integer)
获取多边形的外边缘 ST_ExteriorRing(geometry)
获取多边形内边界个数 ST_NumInteriorRings(geometry)
同上 ST_NumInteriorRing(geometry)
获取多边形的第N个内边界 ST_InteriorRingN(geometry,integer)
获取线的终点 ST_EndPoint(geometry)
获取线的起始点 ST_StartPoint(geometry)
获取几何对象的类型 GeometryType(geometry)
类似上，但是不检查M值，即POINTM对象会被判断为point ST_GeometryType(geometry)
获取点的X坐标 ST_X(geometry)
获取点的Y坐标 ST_Y(geometry)
获取点的Z坐标 ST_Z(geometry)
获取点的M值 ST_M(geometry)

### 几何对象构造函数 ：

参考语义：
Text：WKT
WKB：WKB
Geom:Geometry
M:Multi
Bd:BuildArea



**geojson转geometry：st_geomfromgeojson(geojson)**

Coll:Collection ST_GeomFromText(text,[])

ST_PointFromText(text,[])
ST_LineFromText(text,[])
ST_LinestringFromText(text,[])
ST_PolyFromText(text,[])
ST_PolygonFromText(text,[])
ST_MPointFromText(text,[])
ST_MLineFromText(text,[])
ST_MPolyFromText(text,[])
ST_GeomCollFromText(text,[])
ST_GeomFromWKB(bytea,[])
ST_GeometryFromWKB(bytea,[])
ST_PointFromWKB(bytea,[])
ST_LineFromWKB(bytea,[])
ST_LinestringFromWKB(bytea,[])
ST_PolyFromWKB(bytea,[])
ST_PolygonFromWKB(bytea,[])
ST_MPointFromWKB(bytea,[])
ST_MLineFromWKB(bytea,[])
ST_MPolyFromWKB(bytea,[])
ST_GeomCollFromWKB(bytea,[])
ST_BdPolyFromText(text WKT, integer SRID)
ST_BdMPolyFromText(text WKT, integer SRID)

