---
layout: post
title:  "Android 改写的listview"
date:   2016-4-27 18:08:05
categories: Android
excerpt: 
---

* content
{:toc}

---

#### 为ListView增加下来刷新和上拉加载更多以及滑动删除： 

* 用到的2个插件：官方的SwipeRefreshLayout 、第三方开源的SwipeMenuListView


* ListView 所在主要布局：

        <android.support.v4.widget.SwipeRefreshLayout 
	   	    android:id="@+id/like_SwipeRefreshLayout"
		    android:layout_width="match_parent"
		    android:layout_height="match_parent"
		    android:orientation="vertical">
		    
		    <com.caibo.weidu.modle.SwipeMenuListView
		        android:id="@+id/like_listView"
		        android:layout_width="match_parent"
		        android:layout_height="match_parent"/>
		    
		</android.support.v4.widget.SwipeRefreshLayout>
        
* 下拉刷新：

        refreshLayout = (SwipeRefreshLayout) findViewById(R.id.like_SwipeRefreshLayout);
        //下拉刷新
		refreshLayout.setColorSchemeResources(android.R.color.holo_blue_light,
											android.R.color.holo_red_light,
											android.R.color.holo_green_light,
											android.R.color.holo_orange_light);
		refreshLayout.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
			
			@Override
			public void onRefresh() {
				// TODO Auto-generated method stub
				Log.i("favoriteListUrl", favoriteListUrl);
				excuteTask(favoriteListUrl);    //请求更新数据
				refreshLayout.setRefreshing(false);
			}
			
		});
        
* 滑动删除：

        favListView = (SwipeMenuListView) findViewById(R.id.like_listView);

        //滑动删除
        SwipeMenuCreator creator = new SwipeMenuCreator() {
            @Override
            public void create(SwipeMenu menu) {
                SwipeMenuItem deleteItem = new SwipeMenuItem(getApplicationContext());
                deleteItem.setBackground(new ColorDrawable(Color.rgb(0xF9,
                        0x3F, 0x25)));
                deleteItem.setWidth(dp2px(120));
                deleteItem.setIcon(R.drawable.like_delete_icon);
                menu.addMenuItem(deleteItem);
            }
        };
        
        favListView.setMenuCreator(creator);
//		favListView.setSwipeDirection(SwipeMenuListView.DIRECTION_LEFT);
        favListView.setOnMenuItemClickListener(new OnMenuItemClickListener() {
            @Override
            public boolean onMenuItemClick(final int position, SwipeMenu menu, int index) {
                //取消收藏
                accountId = accounts.get(position).getAccountId();
                removeFavUrl = initUrls.InitRemoveFavoriteUrl(session, deviceId, accountId);
                MyAsyncTask mTaskRev = new MyAsyncTask(removeFavUrl);
                mTaskRev.setOnDataFinishedListener(new onDataFinishedListener() {
                    @Override
                    public void onDataSuccessfully(Object data) {
                        Log.i("remove_data", data.toString());
                        accounts.remove(position);
                        adapter.notifyDataSetChanged();
                        if (accounts.size() == 0) {
                            likenoneRefresh.setVisibility(View.VISIBLE);
                        }
                    }
                });
                mTaskRev.execute("string");
                return false;
            }
        });
        
* 下拉加载更多：

        //上拉加载更多
        favListView.setOnScrollListener(new AbsListView.OnScrollListener() {
            @Override
            public void onScrollStateChanged(AbsListView view, int scrollState) {
                switch (scrollState) {
                    case AbsListView.OnScrollListener.SCROLL_STATE_IDLE:
                        Log.i("onScrollStateChanged", "onScrollStateChanged");
                        //判断滚动到底部
                        if (view.getLastVisiblePosition() == (view.getCount() - 1)) {
                            favListView.addFooterView(footerLayout);    //显示“正在加载”进度条
                        }
                        else {
                            favListView.removeFooterView(footerLayout); //移除“正在加载”进度条
                        }
                }
            }

            @Override
            public void onScroll(AbsListView view, int firstVisibleItem, int visibleItemCount, int totalItemCount) {
            }
        });
        
* 动态生成上拉加载进度条：

        private LinearLayout InitFooterLayout() {
            LinearLayout.LayoutParams mLayoutParams = new LinearLayout.LayoutParams(
                    LinearLayout.LayoutParams.WRAP_CONTENT,
                    LinearLayout.LayoutParams.WRAP_CONTENT);

            LinearLayout.LayoutParams FFlayoutParams = new LinearLayout.LayoutParams(
                    LinearLayout.LayoutParams.MATCH_PARENT,
                    LinearLayout.LayoutParams.MATCH_PARENT);

            LinearLayout layout = new LinearLayout(getContext());
            layout.setOrientation(LinearLayout.HORIZONTAL);
             ProgressBar progressBar = new ProgressBar(mContext, null,android.R.attr.progressBarStyleSmall);//定义进度条的样式
            // 进度条显示位置
            progressBar.setPadding(0, 0, 15, 0);
            // 把进度条加入到layout中
            layout.addView(progressBar, mLayoutParams);
            // 文本内容
            TextView textView = new TextView(getContext());
            textView.setText("加载中...");
            textView.setGravity(Gravity.CENTER_VERTICAL);
            // 把文本加入到layout中
            layout.addView(textView, FFlayoutParams);
            // 设置layout的重力方向，即对齐方式是
            layout.setGravity(Gravity.CENTER);

            // 设置ListView的页脚layout
            LinearLayout loadingLayout = new LinearLayout(getContext());
            loadingLayout.addView(layout, mLayoutParams);
            loadingLayout.setGravity(Gravity.CENTER);

            return loadingLayout;

        }

* 滚回顶部

        在adapter.notifyDataSetChanged();后面加上 merchantList.setSelection(0);
        
        
* 上拉加载的进度条应该放在listview的adapter的getView方法中

* 关于左右侧滑删除和下拉刷新事件冲突的解决方案：

        在SwipeMenuListView中增加监听滑动事件的监听事件：
        swipeMenuListView.setOnSwipeListener(new SwipeMenuListView.OnSwipeListener() {
            @Override
            public void onSwipeStart(int i) {
                refreshLayout.setEnabled(false);    //滑动开始时不能下拉刷新
            }

            @Override
            public void onSwipeEnd(int i) {
                refreshLayout.setEnabled(true);     //滑动结束后可以下拉刷新
            }
        });
        
---

#### listview点击事件失效

        在listview的item中包含有可点击的子控件时，就可能产生listview没法点击的情况.自定义ListViewItem中有Button或者Checkable的子类控件的话，那么默认focus是交给了子控件，而ListView的Item能被选中的基础是它能获取Focus.
        解决办法：对ItemLayout的根控件设置其android:descendantFocusability=”blocksDescendant”即可

---

#### 跳转到手机应用市场

        String mAddress = "market://details?id=" + getPackageName();
        Intent marketIntent = new Intent("android.intent.action.VIEW");
        marketIntent.setData(Uri.parse(mAddress));
        startActivity(marketIntent);

---

#### 软键盘相关android:windowSoftInputMode

        取值：
        【A】stateUnspecified：软键盘的状态并没有指定，系统将选择一个合适的状态或依赖于主题的设置
        【B】stateUnchanged：当这个activity出现时，软键盘将一直保持在上一个activity里的状态，无论是隐藏还是显示
        【C】stateHidden：用户选择activity时，软键盘总是被隐藏
        【D】stateAlwaysHidden：当该Activity主窗口获取焦点时，软键盘也总是被隐藏的
        【E】stateVisible：软键盘通常是可见的
        【F】stateAlwaysVisible：用户选择activity时，软键盘总是显示的状态
        【G】adjustUnspecified：默认设置，通常由系统自行决定是隐藏还是显示
        【H】adjustResize：该Activity总是调整屏幕的大小以便留出软键盘的空间
        【I】adjustPan：当前窗口的内容将自动移动以便当前焦点从不被键盘覆盖和用户能总是看到输入内容的部分

---

> 参考文章：[FragmentTabHost的应用](http://www.colabug.com/thread-1054253-1-1.html)、[竖型TABHOST](http://www.cnblogs.com/shanzei/archive/2012/04/06/2419367.html)

---

### 结语：

坚持每天进步一点点...

---