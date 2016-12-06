---
layout: post
title:  "Android 碰到的问题"
date:   2016-11-01 13:24:05
categories: Android
excerpt: 
---

* content
{:toc}

---

#### Android 碰到的问题记录

---

* 魅族手机连接Android Studio 调试时显示手机未授权，连不上，但是手机USB调试已经开启

    - 重启手机
    - 关闭重新打开USB调试
    - 重启Android Studio

    - 网上查到的方法都没有解决，最后删除系统环境变量下的“ANDROID_SDK_HOME”解决

* 去掉Activity默认的切换动画：

        <style name="introduce" parent="AppBaseThemeNotTranslucent">
            <item name="android:windowAnimationStyle">@style/animation_null</item>
        </style>

        <style name="animation_null" parent="@android:style/Animation.Activity">
            <item name="android:activityOpenEnterAnimation">@null</item>
            <item name="android:activityOpenExitAnimation">@null</item>
            <item name="android:activityCloseEnterAnimation">@null</item>
            <item name="android:activityCloseExitAnimation">@null</item>
            <item name="android:taskOpenEnterAnimation">@null</item>
            <item name="android:taskOpenExitAnimation">@null</item>
            <item name="android:taskCloseEnterAnimation">@null</item>
            <item name="android:taskCloseExitAnimation">@null</item>
            <item name="android:taskToFrontEnterAnimation">@null</item>
            <item name="android:taskToFrontExitAnimation">@null</item>
            <item name="android:taskToBackEnterAnimation">@null</item>
            <item name="android:taskToBackExitAnimation">@null</item>
        </style>

        在需要去掉Activity默认动画的地方引用Style
        <activity android:name=".main.IntroduceActivity"
            android:screenOrientation="portrait"
            android:theme="@style/introduce">
        </activity>

---

* Android Studio导入项目一直卡在Building gradle project info
    
    - AndroidStudio导入项目一直卡在Building gradle project info，实际上是因为你导入的这个项目使用的gradle与你已经拥有的gradle版本不一致，导致需要下载该项目需要的gradle版本，不知是被墙了还是什么原因，反正就是会一直卡住，直至下载完成.

    解决办法：

    - 去官网下载gradle的版本，然后放到本地
    - 直接修改gradle-wrapper.properties文件：找一个能运行的AndroidStudio项目，打开gradle-wrapper.properties，文件目录：项目/gradle/wrapper/gradle-wrapper.properties，复制distributionUrl这一整行的内容，替换对应需要打开的项目即可
        
---

*  JSON中optString和getString的区别:
    
    - optString方法会在对应的key中的值不存在的时候返回一个空字符串或者返回你指定的默认值，但是getString方法会出现空指针异常的错误。

---

* Android Studio 编译不通过，错误：Error:Execution failed for task ':app:clean'. > Unable to delete directory: C:\Users\CB-Ye\...

    - 多次编译试一试
    - Settings --> Instant Run --> 将Enable Instant Run ....选项去掉，重新编译

---

* Unable to add window -- token null is not valid; is your activity running?

    - 出现的原因是fragment中dialog的getActivity()为null

    - dialog的Context不能用application
    - getActivity() 必须在确保Fragment的生命周期方法回调以后调用，最好是在OnResume()方法以后，此时getActivity()不会为null


---

* android.view.WindowManager$BadTokenException: Unable to add window -- token null is not for an application
* android.view.WindowManager$BadTokenException: Unable to add window -- token android.os.BinderProxy@1da4001 is not valid; is your activity running?

    - 主要还是dialog的getActivity() = null，在加载数据时往往需要显示dialog，再这之间一定要确保getActivity() != null

---

* viewpage + fragments的结构，在加了本地缓存的情况下，出现特殊的情况：本地加载以后，网络请求得到的数据和本地不一样，需要重新刷新生成Viewpager + fragments。这个时候就容易报上面的错误。按照逻辑应该重新生成新的viewpager + fragments,但是viewpager + pagerFragmentAdapter会缓存先前的fragment。采用以下方法：

        FragmentAdapter adapter = new FragmentAdapter(getActivity().getSupportFragmentManager(), fragments);

        salePager = resetSalePager(adapter);

        private ViewPager resetSalePager(final SaleFragmentAdapter adapter){


            ViewGroup group = (ViewGroup)salePager.getParent();

            int pagerId = salePager.getId();
            group.removeView(salePager);


            ViewPager viewPager = new ViewPager(getActivity());
            viewPager.setId(pagerId);
            viewPager.setLayoutParams(new ViewGroup.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT));
            viewPager.setAdapter(adapter);
            group.addView(viewPager);

            viewPager.addOnPageChangeListener(pageChangeListener);

            return viewPager;
        }

        //FragmentAdapter采用FragmentStatePagerAdapter
        private class SaleFragmentAdapter extends FragmentStatePagerAdapter {

            private List<? extends Fragment> fragments;

            public SaleFragmentAdapter(FragmentManager fm, List<? extends Fragment> fragments){

                super(fm);
                this.fragments = fragments;
            }

            @Override
            public CharSequence getPageTitle(int position) {
                return fragments != null && position < fragments.size() ? ((PullRequestMoreFragment)fragments.get(position)).getTitle() : "";
            }

            @Override
            public int getItemPosition(Object object) { //返回POSITION_NONE，强制生成新的fragment
                return POSITION_NONE;
            }

            @Override
            public Fragment getItem(int position) {
                return fragments.get(position);
            }

            @Override
            public int getCount() {
                return fragments.size();
            }

            @Override
            public Object instantiateItem(ViewGroup container, final int position) {
                return super.instantiateItem(container, position);
            }

            @Override
            public void destroyItem(ViewGroup container, int position, Object object) {
                container.removeView((View) object);
            }

        }


---

* Activity + PagerSlidingTabStrip + ViewPager + Fragments从后台返回时，Fragemnts被销毁，重新生成失败

    - 一般是应用再后台时间长了，或是系统资源紧张了，所以直接重新生成新的fragments,方法是阻止Activity通过savedInstanceState恢复状态

            @Override
            protected void onCreate(Bundle savedInstanceState) {
                super.onCreate(null);
            }

            @Override
            public void onRestoreInstanceState(Bundle savedInstanceState) {
                Bundle bundle = new Bundle();
                super.onRestoreInstanceState(bundle);

            }

    - 参考文章：[Prevent Fragment recovery in Android]{http://stackoverflow.com/questions/15519214/prevent-fragment-recovery-in-android}

---

> 参考文章：

---

### 结语：

坚持每天进步一点点...

---