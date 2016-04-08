---
layout: post
title:  "Android 异步任务AsyncTask"
date:   2016-4-8 15:08:05
categories: Android
excerpt: 
---

* content
{:toc}

#### Android AsyncTask 

---

* 重写AsyncTask

        public class MyAsyncTask extends AsyncTask<String, Integer, Object> {
	
            private Object appDatas;
            private String url;
            
            onDataFinishedListener onDataFinishedListener;
            
            public MyAsyncTask(String address) {
                this.url = address;
            }
            
            //设置监听，从而可以获取AsyncTask返回的数据
            public void setOnDataFinishedListener(onDataFinishedListener onDataFinishedListener) {
                this.onDataFinishedListener = onDataFinishedListener;
            }
            
            //onPreExecute方法用于在执行后台任务前做一些UI操作  
            @Override
            protected void onPreExecute() {
                
            }
            
            //doInBackground方法内部执行后台任务,不可在此方法内修改UI
            @Override 
            protected Object doInBackground(String... address) {
                try {
                    if (address[0] == "string") {
                        appDatas = okHttp.getAppData(url);
                    }
                    else if (address[0] == "bitmap") {
                        appDatas = okHttp.getBitmap(url);
                    }
                    return appDatas;
        //			Log.i("MyappDatas", appDatas);
                } catch (Exception e) {
                    e.printStackTrace();
                    return null;
                }
            }
            
             //onProgressUpdate方法用于更新进度信息
            @Override
            protected void onProgressUpdate(Integer... progress) {
                
            }
            
            //onPostExecute方法用于在执行完后台任务后更新UI,显示结果
            @Override
            protected void onPostExecute(Object result) {
                
                if (result != null) {
                    onDataFinishedListener.onDataSuccessfully(result);
                }
            }
        }

* 定义接口

        public interface onDataFinishedListener {
            public void onDataSuccessfully(Object data);
        }
        
* 在Activity中调用，引用的是实际项目中的代码段

        try {
			MyAsyncTask mTask = new MyAsyncTask(baseUrl);
			mTask.setOnDataFinishedListener(new onDataFinishedListener() {
				@Override
				public void onDataSuccessfully(Object data) {
					ViewPager pager = (ViewPager) findViewById(R.id.viewPager);
					String account_data = data.toString();
					try {
//					Log.i("account_data", account_data);
						accounts = new ArrayList<Account>();
						JSONObject jsonObject = new JSONObject(account_data);
						jsonAccounts = jsonObject.getJSONObject("data").getJSONArray("accounts");
						for (int i = 0; i < jsonAccounts.length(); i++) {
							accountName = jsonAccounts.getJSONObject(i).getString("a_name");
							accountWxNo = jsonAccounts.getJSONObject(i).getString("a_wx_no");
							accountId = jsonAccounts.getJSONObject(i).getString("a_id");
							accountLogoLink = jsonAccounts.getJSONObject(i).getString("a_logo");
							accountDesc = jsonAccounts.getJSONObject(i).getString("a_desc");
							accountValidReason = jsonAccounts.getJSONObject(i).getString("a_valid_reason");
							accountScore = Integer.valueOf(jsonAccounts.getJSONObject(i).getString("a_rank"));
							Account account = new Account(accountName, accountWxNo, accountId, accountDesc, accountLogoLink, accountScore, accountValidReason);
							accounts.add(account);
						}
						fragment = new AccountFragment(accounts, ChildCatsActivity.this);
						fragments.add(fragment);
						pager.setAdapter(new myPagerAdapter(getSupportFragmentManager(), fragments, titles));
						pager.setCurrentItem(0);
						tabs.setViewPager(pager);
//						Log.i("accounts", accounts.toString());
					} catch (Exception e) {
						e.printStackTrace();
					}
				}
			});
			mTask.execute("string");
		} catch (Exception e) {
			e.printStackTrace();
		}

---


> 参考文章：

---

### 结语：

坚持每天进步一点点...

---