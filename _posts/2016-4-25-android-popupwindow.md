---
layout: post
title:  "Android PopUpWindow"
date:   2016-4-25 15:08:05
categories: Android
excerpt: 
---

* content
{:toc}

### Android PopUpWindow： 

---

#### PopUpWindow和AlertDialog 很像，比较明显的区别就是PopUpWindow可以设置对话框显示的位置，比如在调用相机时在底部弹出的对话框

* 直接贴项目中的用法：
        
        private PopupWindow mPopWindow;
        
        private void showPopupWindow() {
            View convertView = LayoutInflater.from(UserInformationActivity.this).inflate(R.layout.usercenter_takephoto_dialog, null);
            View rootView = LayoutInflater.from(UserInformationActivity.this).inflate(R.layout.usercenter_activity_user_information, null);
            mPopWindow = new PopupWindow(convertView, RelativeLayout.LayoutParams.MATCH_PARENT, RelativeLayout.LayoutParams.WRAP_CONTENT, true);
            mPopWindow.setContentView(convertView);
            //点击空白处消失
            mPopWindow.setFocusable(true);
            mPopWindow.setBackgroundDrawable(new BitmapDrawable());            
            mPopWindow.setOutsideTouchable(true);
            
            //设置背景透明，就像AlertDialog；1f为不更改，0f为完全消失
            backgroundAlpha(0.4f);
            
            //设置监听事件
            RelativeLayout camera = (RelativeLayout) convertView.findViewById(R.id.rl_takephoto_camera);
            RelativeLayout album = (RelativeLayout) convertView.findViewById(R.id.rl_takephoto_album);
            RelativeLayout cancel = (RelativeLayout) convertView.findViewById(R.id.rl_takephoto_cancel);
            camera.setOnClickListener(clickListener);
            album.setOnClickListener(clickListener);
            cancel.setOnClickListener(clickListener);
            
            //设置布局和显示的位置
            mPopWindow.showAtLocation(rootView, Gravity.BOTTOM, 0, 0);
            
            //设置消失监听，把背景改回去
            mPopWindow.setOnDismissListener(new PopupWindow.OnDismissListener() {
                @Override
                public void onDismiss() {
                    backgroundAlpha(1f);
                }
            });
        }
        
        //设置背景透明，就像AlertDialog
        private void backgroundAlpha(float bgAlpha) {
            WindowManager.LayoutParams lp = getWindow().getAttributes();
            lp.alpha = bgAlpha;
            getWindow().setAttributes(lp);
        }

---


> 参考文章：[PopUpWindow使用详解(一)——基本使用](http://blog.csdn.net/harvic880925/article/details/49272285)、[android中popupwindow弹出后，屏幕背景变成半透明](http://blog.csdn.net/chenguang79/article/details/43016519)、[Popupwindow 完美demo](http://www.tuicool.com/articles/6RZBVvI)

---

### 结语：

坚持每天进步一点点...

---