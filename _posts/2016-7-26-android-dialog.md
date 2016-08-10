---
layout: post
title:  "Android 自定义Dialog"
date:   2016-7-26 9:08:05
categories: Android
excerpt: 
---

* content
{:toc}

---

### 自定义Dialog

---

        public class CouponDialog extends Dialog {

            private Item item;
            private Context mContext;
            private FragmentActivity activity;
            private Dialog dialog;

            private ImageButton close;
            private Button getCoupon, buy;
            private TextView couponTitle, buyTitle, couponValue, couponText, itemTitle, itemPrice;
            private ImageView itemImg;
            private LinearLayout itemBuy, couponGet;

            public CouponDialog(Context context) {
                super(context);
                this.mContext = context;
            }

            public CouponDialog(Context context, int theme) {
                super(context, theme);
                this.mContext = context;
            }

            public CouponDialog(FragmentActivity activity, Item item) {
                super(activity);

                this.item = item;
                this.activity = activity;

                initDialog();
            }

            private void initDialog() {
                
                //dialog样式属性
                dialog= new Dialog(getContext(), R.style.sale_coupon_dialog);

                Window dialogWindow = dialog.getWindow();
                WindowManager.LayoutParams lp = dialogWindow.getAttributes();
                lp.width = LinearLayout.LayoutParams.WRAP_CONTENT;
                lp.height = LinearLayout.LayoutParams.WRAP_CONTENT;
                
                //dialogWindow.setGravity(Gravity.BOTTOM);    //显示在底部
                
                dialogWindow.setAttributes(lp);
                
                //dialog布局
                View view = LayoutInflater.from(getContext()).inflate(R.layout.sale_cell_group_dialog, null);

                close = (ImageButton) view.findViewById(R.id.ib_close);
                close.setOnClickListener(clickListener);
                getCoupon = (Button) view.findViewById(R.id.btn_get_coupon);
                getCoupon.setOnClickListener(clickListener);
                buy = (Button) view.findViewById(R.id.btn_item_buy);
                buy.setOnClickListener(clickListener);
                couponGet = (LinearLayout) view.findViewById(R.id.ll_get_coupon);
                couponGet.setOnClickListener(clickListener);
                itemBuy = (LinearLayout) view.findViewById(R.id.ll_item_buy);
                itemBuy.setOnClickListener(clickListener);

                couponTitle = (TextView) view.findViewById(R.id.tv_coupon_title);
                couponText = (TextView) view.findViewById(R.id.tv_coupon_text);
                buyTitle = (TextView) view.findViewById(R.id.tv_buy_title);
                couponValue = (TextView) view.findViewById(R.id.tv_coupon_value);
                itemTitle = (TextView) view.findViewById(R.id.tv_item_title);
                itemPrice = (TextView) view.findViewById(R.id.tv_item_price);
                itemImg = (ImageView) view.findViewById(R.id.iv_item_image);

                couponText.setText("到" + item.getMerchant_name() + "抢优惠券");
                buyTitle.setText("到" + item.getMerchant_name() + "购买商品");

                if (item.getItem_coupon_value() != null && !"".equals(item.getItem_coupon_value())) {
                    couponValue.setText(item.getItem_coupon_value() + "元");
                }
                else {
                    couponValue.setText("未知");
                }

                if (item.getItem_coupon_title() != null && !"".equals(item.getItem_coupon_title())) {
                    couponTitle.setText(item.getItem_coupon_title());
                }
                else {
                    couponTitle.setText("优惠券");
                }

                itemTitle.setText(item.getItem_title());
                itemPrice.setText((item.getItem_price_string()==null?"0.00":item.getItem_price_string())+" ");

                FLDImageLoaderUtil.displayImage(FLDImageFitUtil.fitImage(getContext(), item.getItem_image(), AppUtil.dip2px(activity, 80), AppUtil.dip2px(activity, 80)), itemImg, R.mipmap.placeholder_square);

                dialog.setContentView(view);
                dialog.show();

            }

            private View.OnClickListener clickListener = new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    switch (v.getId()) {
                        case R.id.ib_close:
                            if (dialog != null && dialog.isShowing()) {
                                Log.i("close dialog", "dismiss");
                                dialog.dismiss();
                            }
                            break;
                        case R.id.btn_get_coupon:
                            Intent coupon = new Intent(activity, WebActivity.class);
                            coupon.putExtra("loadUrl", item.getItem_coupon_url());
                            coupon.putExtra("modal", 1);
                            activity.startActivity(coupon);
                            break;
                        case R.id.btn_item_buy:
                            Intent buy = new Intent(activity, WebActivity.class);
                            buy.putExtra("loadUrl", item.getItem_redirect_url());
                            activity.startActivity(buy);
                            dialog.dismiss();
                            break;
                        case R.id.ll_get_coupon:
                            Intent intent = new Intent(activity, WebActivity.class);
                            intent.putExtra("loadUrl", item.getItem_coupon_url());
                            intent.putExtra("modal", 1);
                            activity.startActivity(intent);
                            break;
                        case R.id.ll_item_buy:
                            Intent buyIntent = new Intent(activity, WebActivity.class);
                            buyIntent.putExtra("loadUrl", item.getItem_redirect_url());
                            activity.startActivity(buyIntent);
                            dialog.dismiss();
                            break;
                    }
                }
            };

        }
        
        //dialog属性样式
        <style name="dialog_tran" parent="android:style/Theme.Dialog">
            <item name="android:windowFrame">@null</item>   <!-- 边框 -->
            <item name="android:windowNoTitle">true</item>  <!-- 没有标题 -->
            <item name="android:windowBackground">@android:color/transparent</item>                 <!-- 对话框的内容窗口背景色 -->
            <item name="android:windowIsFloating">false</item>  <!-- 是否浮现在activity之上 -->
            <item name="android:windowContentOverlay">@null</item>  <!-- 是否有覆盖 -->
            <item name="android:windowIsTranslucent">true</item>    <!-- 背景透明 -->
            <item name="android:backgroundDimEnabled">false</item>  //背景变暗
            <item name="android:backgroundDimAmount">0.6</item>                 <!-- 整个屏幕背景的灰度值 -->
        </style>

        <style name="sale_coupon_dialog" parent="dialog_tran">
            //dialog背景
            <item name="android:windowBackground">@drawable/shape_sale_coupondialog_background</item>
            <item name="android:backgroundDimEnabled">true</item>   //背景变暗
            <item name="android:windowIsTranslucent">false</item>   //半透明
        </style>


---

> 参考文章：

---

### 结语：

坚持每天进步一点点...

---