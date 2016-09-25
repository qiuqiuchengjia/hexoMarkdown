---
title: Android美女拼图游戏
date: 2016-09-12 19:35:17
tags: Android游戏
categories: Android游戏
---

## 概述 

- [游戏下载试玩](http://o99dg8ap9.bkt.clouddn.com/Android%E7%BE%8E%E5%A5%B3%E6%8B%BC%E5%9B%BE.apk)

- [Github](https://github.com/qiuchengjia/Android-BeautyGame) 喜欢的同学可以Star一下，非常感谢

- 把图片切分很多份，点击交换拼成一张完整的；这样关卡也很容易设计，3*3；4*4；5*5；6*6；一直下去

- 效果

<center>![](http://o99dg8ap9.bkt.clouddn.com/Android%E7%BE%8E%E5%A5%B3%E6%8B%BC%E5%9B%BE1.gif)</center>

- 加了个切换动画，效果还是不错的，其实游戏就是自定义了一个控件，下面我们开始自定义之旅

<!-- more -->

## 游戏的设计

首先我们分析下如何设计这款游戏：

1. 我们需要一个容器，可以放这些图片的块块，为了方便，我们准备使用RelativeLayout配合addRule实现

2. 每个图片的块块，我们准备使用ImageView

3. 点击交换，我们准备使用传统的TranslationAnimation来实现

有了初步的设计，感觉这游戏so easy~

## 游戏布局的实现

首先，我们准备实现能够把一张图片，切成n*n份，放在指定的位置；
我们只需要设置n这个数字，然后根据布局的宽或者高其中的小值，除以n，减去一些边距就可以得到我们ImageView的宽和高了~~

### 构造方法

```java
/** 
     * 设置Item的数量n*n；默认为3 
     */  
    private int mColumn = 3;  
    /** 
     * 布局的宽度 
     */  
    private int mWidth;  
    /** 
     * 布局的padding 
     */  
    private int mPadding;  
    /** 
     * 存放所有的Item 
     */  
    private ImageView[] mGamePintuItems;  
    /** 
     * Item的宽度 
     */  
    private int mItemWidth;  
  
    /** 
     * Item横向与纵向的边距 
     */  
    private int mMargin = 3;  
      
    /** 
     * 拼图的图片 
     */  
    private Bitmap mBitmap;  
    /** 
     * 存放切完以后的图片bean 
     */  
    private List<ImagePiece> mItemBitmaps;  
      
    private boolean once;  
      
    public GamePintuLayout(Context context)  {  
        this(context, null);  
    }  
  
    public GamePintuLayout(Context context, AttributeSet attrs)  {  
        this(context, attrs, 0);  
    }  
  
  /**
     * 构造函数，用来初始化
     * @param context  the context
     * @param attrs    the attrs
     * @param defStyle the def style
     * @author qiu  博客：www.qiuchengjia.cn 时间：2016-09-12
     */
    public GamePintuLayout(Context context, AttributeSet attrs, int defStyle)  {  
        super(context, attrs, defStyle);  
  
   //把设置的margin值转换为dp
        mMargin = (int) TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP,  
                mMargin, getResources().getDisplayMetrics());  
        // 设置Layout的内边距，四边一致，设置为四内边距中的最小值  
        mPadding = min(getPaddingLeft(), getPaddingTop(), getPaddingRight(),  
                getPaddingBottom());  
    }  
```

- 构造方法里面，我们得到把设置的margin值转化为dp；获得布局的padding值；整体是个正方形，所以我们取padding四个方向中的最小值；
至于margin，作为Item之间的横向与纵向的间距，你喜欢的话可以抽取为自定义属性~~

### onMeasure

```java
/**
     * 用来设置设置自定义的View的宽高，
     * @param widthMeasureSpec  the width measure spec
     * @param heightMeasureSpec the height measure spec
     * @author qiu  博客：www.qiuchengjia.cn 时间：2016-09-12
     */
@Override  
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec)  {  
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);  
      
        // 获得游戏布局的边长  
        mWidth = Math.min(getMeasuredHeight(), getMeasuredWidth());  
  
        if (!once)  {  
            initBitmap();  
            initItem();  
        }  
        once = true;  
        setMeasuredDimension(mWidth, mWidth);  
    }  
```

- onMeasure里面主要就是获得到布局的宽度，然后进行图片的准备，以及初始化我们的Item，为Item设置宽度和高度

- initBitmap自然就是准备图片了：

```java
/**
  * 初始化bitmap
  * @author qiu  博客：www.qiuchengjia.cn 时间：2016-09-12
  */
private void initBitmap()  {  
        if (mBitmap == null)  
            mBitmap = BitmapFactory.decodeResource(getResources(),  
                    R.drawable.aa);  
  
        mItemBitmaps = ImageSplitter.split(mBitmap, mColumn);  
  
   //对图片进行排序
        Collections.sort(mItemBitmaps, new Comparator<ImagePiece>(){  
            @Override  
            public int compare(ImagePiece lhs, ImagePiece rhs){  
            //我们使用random随机比较大小
                return Math.random() > 0.5 ? 1 : -1;  
            }  
        });  
    }  
```

- 我们这里如果没有设置mBitmap就准备一张备用图片，然后调用ImageSplitter.split将图片切成n * n 返回一个List<ImagePiece>
切完以后，我们需要将顺序打乱，所以我们调用了sort方法，至于比较器，我们使用random随机比较大小，这样我们就完成了我们的乱序操作，赞不赞~~


```java
/**
 * Description: 图片切片类
 * Data：2016/9/11-19:53
 * Blog：www.qiuchengjia.cn
 * Author: qiu
 */
public class ImageSplitter  {  
    /** 
     * 将图片切成 , piece *piece 
     * @param bitmap 
     * @param piece 
     * @return 
     */  
    public static List<ImagePiece> split(Bitmap bitmap, int piece){  
  
        List<ImagePiece> pieces = new ArrayList<ImagePiece>(piece * piece);  
  
        int width = bitmap.getWidth();  
        int height = bitmap.getHeight();  
  
        Log.e("TAG", "bitmap Width = " + width + " , height = " + height);  
        int pieceWidth = Math.min(width, height) / piece;  
  
        for (int i = 0; i < piece; i++){  
            for (int j = 0; j < piece; j++){  
                ImagePiece imagePiece = new ImagePiece();  
                imagePiece.index = j + i * piece;  
                int xValue = j * pieceWidth;  
                int yValue = i * pieceWidth;  
                  
                imagePiece.bitmap = Bitmap.createBitmap(bitmap, xValue, yValue,  
                        pieceWidth, pieceWidth);  
                pieces.add(imagePiece);  
            }  
        }  
        return pieces;  
    }  
}  
```

```java
/**
 * Description: 图片bean
 * Data：2016/9/11-19:54
 * Blog：www.qiuchengjia.cn
 * Author: qiu
 */
public class ImagePiece  
{  
    public int index = 0;  
    public Bitmap bitmap = null;  
}  
```

- 没撒说的就是一个根据宽度高度，和n，来切图保存的过程~~
ImagePiece保存的图片以及索引，话说这两个类还是我无意中在网上发现的~~
图片到此就准备好了，现在看Item的生成已经设置宽高，即initItems


```java
 /**
   * 初始化每一个item
   * @author qiu  博客：www.qiuchengjia.cn 时间：2016-09-12
   */
private void initItem()  {  
        // 获得Item的宽度  
        int childWidth = (mWidth - mPadding * 2 - mMargin * 
        (mColumn - 1)) / mColumn;  
        mItemWidth = childWidth;  
  
        mGamePintuItems = new ImageView[mColumn * mColumn];  
        // 放置Item  
        for (int i = 0; i < mGamePintuItems.length; i++) {  
            ImageView item = new ImageView(getContext());  
  
            item.setOnClickListener(this);  
  
            item.setImageBitmap(mItemBitmaps.get(i).bitmap);  
            mGamePintuItems[i] = item;  
            item.setId(i + 1);  
            item.setTag(i + "_" + mItemBitmaps.get(i).index);  
  
            RelativeLayout.LayoutParams lp =
                new LayoutParams(mItemWidth,  
                    mItemWidth);  
            // 设置横向边距,不是最后一列  
            if ((i + 1) % mColumn != 0)  {  
                lp.rightMargin = mMargin;  
            }  
            // 如果不是第一列  
            if (i % mColumn != 0)  {  
                lp.addRule(RelativeLayout.RIGHT_OF,//  
                        mGamePintuItems[i - 1].getId());  
            }  
            // 如果不是第一行，//设置纵向边距，非最后一行  
            if ((i + 1) > mColumn)  {  
                lp.topMargin = mMargin;  
                lp.addRule(RelativeLayout.BELOW,//  
                        mGamePintuItems[i - mColumn].getId());  
            }  
            addView(item, lp);  
        }  
    }  
```

- 可以看到我们的Item宽的计算：childWidth = (mWidth - mPadding * 2 - mMargin * (mColumn - 1) ) / mColumn;
容器的宽度，除去自己的内边距，除去Item间的间距，然后除以Item一行的个数就得到了Item的宽~~
接下来，就是遍历生成Item，根据他们的位置设置Rule，自己仔细看下注释~~

__注意两点：__

- 我们为Item设置了setOnClickListener，这个当然，因为我们的游戏就是点Item么~

- 还有我们为Item设置了Tag：item.setTag(i + "_" + mItemBitmaps.get(i).index);  
tag里面存放了index，也就是正确的位置；还有i，i 可以帮助我们在mItemBitmaps找到当前的Item的图片：（mItemBitmaps.get(i).bitmap）

- 到此，我们游戏的布局的代码就结束了~~~

- 然后我们在布局文件里面声明下：

```html
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" >

    <game.qiu.com.beautygame.GamePintuLayout
        android:id="@+id/id_gameview"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:layout_centerInParent="true"
        android:padding="5dp" >
    </game.qiu.com.beautygame.GamePintuLayout>

</RelativeLayout>
```

- Activity里面记得设置这个布局~~

- 现在的效果是：

<center>![](http://o99dg8ap9.bkt.clouddn.com/Android%E7%BE%8E%E5%A5%B3%E6%8B%BC%E5%9B%BE5.png)</center>


## 游戏的切换效果

### 初步的切换

- 还记得我们都给Item添加了onClick的监听么~~
现在我们需要实现，点击两个Item，他们的图片能够发生交换~
那么，我们需要两个成员变量来存储这两个Item，然后再去交换

```java
/**
  * 记录第一次点击的ImageView
  */
private ImageView mFirst;  
/**
  * 记录第二次点击的ImageView
  */
private ImageView mSecond;  
/**
  * 点击事件
  * @param view the view
  * @author qiu  博客：www.qiuchengjia.cn 时间：2016-09-12
  */  
@Override  
public void onClick(View v)  {  
    /** 
     * 如果两次点击是同一个 
     */  
    if (mFirst == v)  {  
        mFirst.setColorFilter(null);  
        mFirst = null;  
        return;  
    }  
    //点击第一个Item  
    if (mFirst == null)  {  
        mFirst = (ImageView) v;  
        mFirst.setColorFilter(Color.parseColor("#55FF0000"));  
    } else//点击第二个Item  
    {  
        mSecond = (ImageView) v;  
        exchangeView();  
    }  
  
}  
```

- 点击第一个，通过setColorFilter设置下选中效果，再次点击另一个，那我们就准备调用exchangeView进行交换图片了，当然这个方法我们还没写，先放着~
如果两次点击同一个，去除选中效果，我们就当什么都没发生

- 接下来，我们来实现exchangeView：

```java
/**
  * 交换两个Item图片 
  * @author qiu  博客：www.qiuchengjia.cn 时间：2016-09-12
  */
 private void exchangeView()  {  
                
        mFirst.setColorFilter(null);  
        String firstTag = (String) mFirst.getTag();  
        String secondTag = (String) mSecond.getTag();  
          
        //得到在list中索引位置  
        String[] firstImageIndex = firstTag.split("_");  
        String[] secondImageIndex = secondTag.split("_");  
          
        mFirst.setImageBitmap(mItemBitmaps.get(Integer  
                .parseInt(secondImageIndex[0])).bitmap);  
        mSecond.setImageBitmap(mItemBitmaps.get(Integer  
                .parseInt(firstImageIndex[0])).bitmap);  
  
        mFirst.setTag(secondTag);  
        mSecond.setTag(firstTag);  
          
        mFirst = mSecond = null;  
  
 }  
```

- 应该还记得我们之前的setTag吧，忘了，返回去看看，我们还说注意来着~
通过getTag，拿到在List中是索引，然后得到bitmap进行交换设置，最后交换tag；
到此我们的交换效果写完了，我们的游戏可以完了~~效果是这样的：

<center>![](http://o99dg8ap9.bkt.clouddn.com/Android%E7%BE%8E%E5%A5%B3%E6%8B%BC%E5%9B%BE3.gif)</center>

- 可以看到我们已经可以玩了，至于为什么不用清爽的风景图，是因为，实在是看不出来那块对那块，还是妹子直观~
大家肯定会吐槽，我擦，动画切换呢，明明不是两个飞过去交换位置么，尼玛这算什么
也是，对与程序我们要有追求，下面我们来添加动画切换效果~~

### 无缝的动画切换

- 我们先聊聊怎么添加，我准备使用TranslationAnimation，然后两个Item的top，left也很容器获取；
但是，要明白，我们实际上，Item只是setImage发生了变化，Item的位置没有变；
我们现在需要动画移动效果，比如A移动到B，没问题，移动完成以后，Item得回去吧，但是图片并没有发生变化，我们还是需要手动setImage
这样造成了一个现象，动画切换效果有了，但是最后还是会有一闪，是我们切换图片造成的；
为了避免上述现象，能够完美的做到切换效果，这里我们引入一个动画图层，专门做动画效果，有点类似ps的图层，下面看我们怎么做；

```java
/** 
  * 动画运行的标志位 
  */  
private boolean isAniming;  
/** 
  * 动画层 
  */  
private RelativeLayout mAnimLayout;  
      
/**
  * 交换两个Item图片
  * @author qiu  博客：www.qiuchengjia.cn 时间：2016-09-12
  */
private void exchangeView(){  
        mFirst.setColorFilter(null);  
        setUpAnimLayout();  
        // 添加FirstView  
        ImageView first = new ImageView(getContext());  
        first.setImageBitmap(mItemBitmaps  
                .get(getImageIndexByTag((String) mFirst.getTag())).bitmap);  
        LayoutParams lp = new LayoutParams(mItemWidth, mItemWidth);  
        lp.leftMargin = mFirst.getLeft() - mPadding;  
        lp.topMargin = mFirst.getTop() - mPadding;  
        first.setLayoutParams(lp);  
        mAnimLayout.addView(first);  
        // 添加SecondView  
        ImageView second = new ImageView(getContext());  
        second.setImageBitmap(mItemBitmaps  
                .get(getImageIndexByTag((String) mSecond.getTag())).bitmap);  
        LayoutParams lp2 = new LayoutParams(mItemWidth, mItemWidth);  
        lp2.leftMargin = mSecond.getLeft() - mPadding;  
        lp2.topMargin = mSecond.getTop() - mPadding;  
        second.setLayoutParams(lp2);  
        mAnimLayout.addView(second);  
  
        // 设置动画  
        TranslateAnimation anim = new TranslateAnimation(0, mSecond.getLeft()  
                - mFirst.getLeft(), 0, mSecond.getTop() - mFirst.getTop());  
        anim.setDuration(300);  
        anim.setFillAfter(true);  
        first.startAnimation(anim);  
  
        TranslateAnimation animSecond = new TranslateAnimation(0,  
                mFirst.getLeft() - mSecond.getLeft(), 0, mFirst.getTop()  
                        - mSecond.getTop());  
        animSecond.setDuration(300);  
        animSecond.setFillAfter(true);  
        second.startAnimation(animSecond);  
        // 添加动画监听  
        anim.setAnimationListener(new AnimationListener(){  
  
            @Override  
            public void onAnimationStart(Animation animation){  
                isAniming = true;  
                mFirst.setVisibility(INVISIBLE);  
                mSecond.setVisibility(INVISIBLE);  
            }  
  
            @Override  
            public void onAnimationRepeat(Animation animation){  
  
            }  
  
            @Override  
            public void onAnimationEnd(Animation animation){  
                String firstTag = (String) mFirst.getTag();  
                String secondTag = (String) mSecond.getTag();  
  
                String[] firstParams = firstTag.split("_");  
                String[] secondParams = secondTag.split("_");  
  
                mFirst.setImageBitmap(mItemBitmaps.get(Integer  
                        .parseInt(secondParams[0])).bitmap);  
                mSecond.setImageBitmap(mItemBitmaps.get(Integer  
                        .parseInt(firstParams[0])).bitmap);  
  
                mFirst.setTag(secondTag);  
                mSecond.setTag(firstTag);  
                mFirst.setVisibility(VISIBLE);  
                mSecond.setVisibility(VISIBLE);  
                mFirst = mSecond = null;  
                mAnimLayout.removeAllViews();  
                                //checkSuccess();  
                isAniming = false;  
            }  
        });  
  
    }  
  
    /** 
     * 创建动画层 
     */  
    private void setUpAnimLayout(){  
        if (mAnimLayout == null){  
            mAnimLayout = new RelativeLayout(getContext());  
            addView(mAnimLayout);  
        }  
  
    }  
      
    private int getImageIndexByTag(String tag){  
        String[] split = tag.split("_");  
        return Integer.parseInt(split[0]);  
  
    }  
```

- 开始交换时，我们创建一个动画层，然后在这一层上添加上两个一模一样的Item，把原来的Item隐藏了，然后尽情的进行动画切换，setFillAfter为true~
动画完毕，我们已经悄悄的把Item的图片交换了，直接显示出来。这样就完美的切换了：

__大致过程：__

1. A ，B隐藏 

2.  A副本动画移动到B的位置；B副本移动到A的位置

3. A把图片设置为B，把B副本移除，A显示，这样就完美切合了，用户感觉是B移动过去的

4. B同上

- 现在我们的效果：

<center>![](http://o99dg8ap9.bkt.clouddn.com/Android%E7%BE%8E%E5%A5%B3%E6%8B%BC%E5%9B%BE6.gif)</center>

- 现在效果满意了把~~为了防止用户狂点，在onClick里面添加一句：

```java
@Override  
    public void onClick(View v)  
    {  
        // 如果正在执行动画，则屏蔽  
        if (isAniming)  
            return;  
```

- 到此我们的动画的切换，已经完美结束了~~
切换时，我们是不是应该判断是否成功了~~

## 游戏胜利的判断

- 我们在切换完成，进行checkSuccess();的判断；好在我们把图片的正确的顺序存在tag里面~~

```java
/**
  * 用来判断游戏是否成功
  * @author qiu  博客：www.qiuchengjia.cn 时间：2016-09-12
  */
private void checkSuccess(){  
        boolean isSuccess = true;  
        for (int i = 0; i < mGamePintuItems.length; i++){  
            ImageView first = mGamePintuItems[i];  
            Log.e("TAG", getIndexByTag((String) first.getTag()) + "");  
            if (getIndexByTag((String) first.getTag()) != i){  
                isSuccess = false;  
            }  
        }  
  
        if (isSuccess){  
            Toast.makeText(getContext(), "Success , Level Up !",  
                    Toast.LENGTH_LONG).show();  
            // nextLevel();  
        }  
    }  
  
    /** 
     * 获得图片的真正索引 
     * @param tag 
     * @return 
     */  
    private int getIndexByTag(String tag){  
        String[] split = tag.split("_");  
        return Integer.parseInt(split[1]);  
    }  
```

- 很简单，遍历所有的Item，根据Tag拿到真正的索引和当然顺序比较，完全一致则胜利~~胜利以后进入下一关

- 至于下一关的代码：

```java
public void nextLevel(){  
        this.removeAllViews();  
        mAnimLayout = null;  
        mColumn++;  
        initBitmap();  
        initItem();  
    }  
```

## 总结

- ok，到此我们的游戏结束了，我来带大家闯个关：

<center>![](http://o99dg8ap9.bkt.clouddn.com/Android%E7%BE%8E%E5%A5%B3%E6%8B%BC%E5%9B%BE7.gif)</center>

### 源码下载

- [传送门](http://o99dg8ap9.bkt.clouddn.com/Android%E7%BE%8E%E5%A5%B3%E6%B8%B8%E6%88%8F-BeautyGame.zip)

## 参考资料

- [Android 实战美女拼图游戏 你能坚持到第几关](http://blog.csdn.net/lmj623565791/article/details/40595385)
