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

        public class MainActivity extends Activity {
	
            private TabHost tabHost;
            
            private String title[] = {"公众号", "喜欢", "更多"}; #标签的题目
            private int image[] = {R.drawable.account_select, R.drawable.like_normal, R.drawable.more_normal};#标签图片
            private int xml[] = {R.drawable.account_selector, R.drawable.like_selector, R.drawable.more_selector};#标签的selector文件
            
            @Override
            protected void onCreate(Bundle savedInstanceState) {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.activity_main);
                
                initTabView();
            }
            
            private void initTabView() {
                this.tabHost = (TabHost) findViewById(R.id.mytabhost);
                tabHost.setup();
                
                for (int i=0; i<title.length; i++){
                    //实例化一个view作为标签的布局，R.layout.tab_layout为导航栏的布局
                    View view = View.inflate(this, R.layout.tab_layout, null);
                    
                    //设置imageView/textView
                    ImageView imageView = (ImageView) view.findViewById(R.id.image);
                    imageView.setImageDrawable(getResources().getDrawable(xml[i]));
                    TextView textView = (TextView) view.findViewById(R.id.title);
                    textView.setText(title[i]);
                    
                    
                    //设置跳转activity
                    //Intent intent = new Intent(this, MainActivity.class);
                    
                    //载入view对象
                    TabSpec spec = tabHost.newTabSpec(title[i]).setIndicator(view).setContent(R.id.tab);
                    
                    //添加到选项卡
                    tabHost.addTab(spec);
                }
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


> 参考文章：

---

### 结语：

坚持每天进步一点点...

---