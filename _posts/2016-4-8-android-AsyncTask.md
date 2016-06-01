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

#### 带post请求和get请求的AsyncTask

        private void connect(final String url, final Map<String, String> params, final int request_type, final byte[] imageData, final String imagePrefix){
        
            //params是参数列表，imageData是Bitmap转换成的字节数组，imagePrefix是图片后缀，如png、jpg等

            new AsyncTask<String, Void, String>() {

                @Override
                protected void onPreExecute() {
                    super.onPreExecute();
                }

                @Override
                protected String doInBackground(String... param) {

                    OkHttpClient client = new OkHttpClient();       //OkHttp3

                    Request request = null;
                    if(request_type==GET){

                        request = new Request.Builder()
                                .url(url)
                                .build();
                    }else if(request_type==POST){

                        MultipartBody.Builder bodyBuilder = new MultipartBody.Builder();
                        bodyBuilder.setType(MultipartBody.FORM);
                        for (Map.Entry<String, String> entry : params.entrySet()) {
                            bodyBuilder.addFormDataPart(entry.getKey(),entry.getValue());
                        }
                        if(imageData!=null && imageData.length>0){
                            String prefix = imagePrefix;
                            String mimeType = "image/png";
                            if("png".equals(prefix)){
                                mimeType = "image/png";
                            }else if("jpg".equals(prefix)||"jpeg".equals(prefix)){
                                mimeType = "image/jpeg";
                            }else if("gif".equals(prefix)){
                                mimeType = "image/gif";
                            }
                            bodyBuilder.addFormDataPart("myfile", "temp."+prefix, RequestBody.create(MediaType.parse(mimeType), imageData));
                        }

                        RequestBody formBody = bodyBuilder.build();
                        request = new Request.Builder()
                                .url(url)
                                .post(formBody)
                                .build();

    //                    FormBody.Builder bodyBuilder = new FormBody.Builder();
    //                    for (Map.Entry<String, String> entry : params.entrySet()) {
    //                        bodyBuilder.add(entry.getKey(),entry.getValue());
    //                    }
    //                    RequestBody formBody = bodyBuilder.build();
    //                    request = new Request.Builder()
    //                            .url(url)
    //                            .post(formBody)
    //                            .build();
                    }else{
                        return null;
                    }

                    Response response = null;
                    String result = null;
                    try {
                        response = client.newCall(request).execute();
                        if(response!=null && response.isSuccessful()) {
                            result = response.body().string();
                        }else {
                            return "0";
                        }
                    }catch (Exception e){
                        e.printStackTrace();
                    }
                    return result;
                }


                @Override
                protected void onPostExecute(String result) {
                    super.onPostExecute(result);

                    if("0".equals(result)) {
                        if(delegate!=null){
                            delegate.receiveFail(DataUtil.this, "网络链接失败，请稍后重试");
                        }
                    }else if(result==null) {
                        if(delegate!=null){
                            delegate.receiveFail(DataUtil.this, "数据请求有误，请重试");
                        }
                    }else{
                        if (delegate != null) {
                            delegate.receiveSuccess(DataUtil.this, result);
                        }
                    }
                }
            }.execute();
        }
        
        //将Bitmap转换成字节数组
        public static byte[] Bitmap2Bytes(Bitmap bm) {
            ByteArrayOutputStream baos = new ByteArrayOutputStream();
            bm.compress(Bitmap.CompressFormat.PNG, 100, baos);
            return baos.toByteArray();
        }

---


> 参考文章：

---

### 结语：

坚持每天进步一点点...

---