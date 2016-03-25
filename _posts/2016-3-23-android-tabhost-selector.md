---
layout: post
title:  "Android 底部导航栏"
date:   2016-3-23 11:08:05
categories: Android
excerpt: 
---

* content
{:toc}

### Android 底部导航栏的实现主要用到tabHost和selector

---

#### tabHost

* 导航栏布局文件layout       
        
        <TabHost xmlns:android="http://schemas.android.com/apk/res/android"
            android:id="@+id/mytabhost"
            android:layout_width="match_parent"
            android:layout_height="match_parent">
            
            <RelativeLayout 
                android:layout_width="match_parent"
                android:layout_height="match_parent">
                
                <FrameLayout 
                    android:id="@android:id/tabcontent"
                    android:layout_width="match_parent"
                    android:layout_height="match_parent">
                    
                    <LinearLayout 
                        android:id="@+id/tab"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent" >
                        
                        <TextView 
                            android:id="@+id/textView1"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="hello world" />
                        
                    </LinearLayout>
                </FrameLayout>
                
                <TabWidget 
                    android:id="@android:id/tabs"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:background="#000000" 
                    android:layout_alignParentBottom="true">
                    
                </TabWidget>
                
            </RelativeLayout>
            
        </TabHost>

* 用相对布局RelativeLayout,通过设置TabWidget的属性android:layout_alignParentBottom="true"实现导航栏固定在底部,同时要设置layout_height属性为wrap_content

* TabWidget即为标签，FrameLayout为导航栏上部的内容

* activity活动代码

        public class MainActivity extends ActivityGroup {
	
            private TabHost tabHost;

            private TextView tab_name;

            @Override
            protected void onCreate(Bundle savedInstanceState) {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.activity_main);
                
                tab_name = (TextView) findViewById(R.id.tab_name);
                this.tabHost = (TabHost) findViewById(R.id.mytabhost);
                tabHost.setup(this.getLocalActivityManager());
                
                tabHost.addTab(tabHost.newTabSpec("公众号").setIndicator(initView("公众号", R.drawable.account_selector)).setContent(new Intent(this, AccountActivity.class)));
                tabHost.addTab(tabHost.newTabSpec("喜欢").setIndicator(initView("喜欢", R.drawable.like_selector)).setContent(new Intent(this, LikeActivity.class)));
                tabHost.addTab(tabHost.newTabSpec("更多").setIndicator(initView("更多", R.drawable.more_selector)).setContent(new Intent(this, MoreActivity.class)));
                tabHost.setCurrentTab(0);
                
            }

            private View initView(String name, int drawableId){
                View view = View.inflate(this, R.layout.tab_layout, null);
                ImageView imageView = (ImageView) view.findViewById(R.id.image);
                TextView textView = (TextView) view.findViewById(R.id.title);
                imageView.setImageDrawable(getResources().getDrawable(drawableId));
                textView.setText(name);
                return view;
            }
        
---

#### selector

* 首先要在res/drawable/目录下建立对应的xml文件

* 然后图片的话就是在上面的java代码中imageView.setImageDrawable(getResources().getDrawable(xml[i]))来引用

* 文字要跟着变颜色的话除了要编辑res/drawable/目录下对应的xml文件，还有就是在导航栏的布局文件R.layout.tab_layout中设置对应的TextView属性android:textColor="@drawable/text_selector" 

* res/drawable/目录下对应的标签图片xml文件

        <?xml version="1.0" encoding="utf-8"?>
        <selector xmlns:android="http://schemas.android.com/apk/res/android" >
            
            #默认的图片，其他的不是默认的标签不需要
            <item android:state_focused="true" android:drawable="@drawable/account_select" />
            
            <item android:state_selected="true" android:drawable="@drawable/account_select" />
            
            <item android:state_selected="false" android:drawable="@drawable/account_normal" />
            

        </selector>
     
* res/drawable/目录下对应的标签文字xml文件

        <?xml version="1.0" encoding="utf-8"?>
        <selector xmlns:android="http://schemas.android.com/apk/res/android" >
            
            <item  android:state_selected="true" android:color="#30d94c"/>
            
            <item  android:state_selected="false" android:color="#FFFFFF"/>
            
        </selector>

---


> 参考文章：[Selector中的各种状态详解](http://blog.csdn.net/whyrjj3/article/details/7852761)、[Android Tabhost使用（展示不同的Tab页）](http://blog.csdn.net/renguichao/article/details/7667264)

---

### 结语：

坚持每天进步一点点...

---