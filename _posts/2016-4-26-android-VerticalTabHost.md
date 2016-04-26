---
layout: post
title:  "Android 垂直的TabHost"
date:   2016-4-26 17:08:05
categories: Android
excerpt: 
---

* content
{:toc}

---

#### 通过改写TabwWigdet,用于实现左侧固定的导航栏,直接贴项目中的用法： 

* 改写TabwWigdet： 
        
        public class VerticalTabwWigdet extends TabWidget{
            private Resources res;

            public VerticalTabwWigdet(Context context, AttributeSet attrs) {
                super(context, attrs);
                res = context.getResources();
                setOrientation(LinearLayout.VERTICAL);
            }

            @Override
            public void addView(View child) {
                LinearLayout.LayoutParams lp = new LayoutParams(LayoutParams.MATCH_PARENT, LayoutParams.WRAP_CONTENT, 1.0f);
                lp.setMargins(0,0,0,0);
                child.setLayoutParams(lp);
                super.addView(child);
            }
        }
        
* tabhost布局：
        
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical">

            <android.support.v4.app.FragmentTabHost
                xmlns:android="http://schemas.android.com/apk/res/android"
                android:id="@android:id/tabhost"
                android:layout_width="match_parent"
                android:layout_height="match_parent">

                <!-- 选用LinearLayout布局以及设置 FrameLayout的属性layout_height="0dp"、
                android:layout_weight="1"以防tab选项卡盖住listView中的内容 -->
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:orientation="horizontal">
                    
                    //实现滚动
                    <ScrollView
                        android:layout_width="wrap_content"
                        android:layout_height="match_parent"
                        android:scrollbars="none">

                        <com.caibo_inc.fuliduo.main.merchant.VerticalTabwWigdet
                            android:id="@android:id/tabs"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:background="#bfbfbf" >
                        </com.caibo_inc.fuliduo.main.merchant.VerticalTabwWigdet>

                    </ScrollView>

                    <FrameLayout
                        android:id="@android:id/tabcontent"
                        android:layout_width="0dp"
                        android:layout_height="match_parent"
                        android:layout_weight="1">
                    </FrameLayout>

                </LinearLayout>

            </android.support.v4.app.FragmentTabHost>

        </LinearLayout>
        
* 在Fragment中嵌套TabHost:

         private String tabHostTitle[] = {"推荐商家","餐饮连锁","网上商城","银行支付","金融理财","家用电器","电脑办公","手机数码","母婴童装","图书音像","品牌男装","潮流女装"};

        fragmentTabHost = (FragmentTabHost) rootView.findViewById(android.R.id.tabhost);
        fragmentTabHost.setup(getActivity(),getChildFragmentManager(),android.R.id.tabcontent);

        for (int i = 0; i < tabHostTitle.length; i++) {
            View merchantListView = View.inflate(getActivity(),R.layout.cell_merchant_tabhost, null);
            ((TextView) merchantListView.findViewById(R.id.tv_merchant_name)).setText(tabHostTitle[i]);
            TabHost.TabSpec merchantSpec = fragmentTabHost.newTabSpec(tabHostTitle[i]).setIndicator(merchantListView);
            fragmentTabHost.addTab(merchantSpec, SaleFragment.class, null);
        }
        fragmentTabHost.setCurrentTab(0);

---


> 参考文章：[FragmentTabHost的应用](http://www.colabug.com/thread-1054253-1-1.html)、[竖型TABHOST](http://www.cnblogs.com/shanzei/archive/2012/04/06/2419367.html)

---

### 结语：

坚持每天进步一点点...

---