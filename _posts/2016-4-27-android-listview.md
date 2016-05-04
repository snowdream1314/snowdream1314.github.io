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
        
---

> 参考文章：[FragmentTabHost的应用](http://www.colabug.com/thread-1054253-1-1.html)、[竖型TABHOST](http://www.cnblogs.com/shanzei/archive/2012/04/06/2419367.html)

---

### 结语：

坚持每天进步一点点...

---