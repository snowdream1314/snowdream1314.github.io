---
layout: post
title:  "Android Fragment横竖屏适配"
date:   2016-10-13 16:44:05
categories: Android
excerpt: 
---

* content
{:toc}

---

#### Android Fragment横竖屏/宽屏适配

> 这里有一个使用两个fragment创建双区域布局的activity的范例。这个activity下面包含的一个fragment用来显示莎士比亚戏剧列表，另一个fragment则用来在列表项被选中时显示该戏剧的概要。它也表明了如何基于不同屏幕配置提供不同的fragment配置。

* 主要构成：MainActivity主活动,TitleFragment展示列表,DetailActivity展示概要(横屏或是大屏),DetailsActivity展示概要(竖屏时).

* 在手机足够容下2个fragment时，点击左边TitleFragment列表，右边DetailFragment展示概要；在手机屏幕不足以显示2个Fragment时,点击点击左边TitleFragment列表跳转到DetailsActivity展示概要

---

* MainActivity:

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
         
             etContentView(R.layout.fragment_layout);
        }

* fragment_layout.xml

        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            android:orientation="horizontal"
            android:layout_width="match_parent" android:layout_height="match_parent">

            <fragment class="com.example.android.apis.app.FragmentLayout$TitlesFragment"
                    android:id="@+id/titles" 
                    android:layout_weight="1"
                    android:layout_width="0px" 
                    android:layout_height="match_parent" />

            <FrameLayout 
                    android:id="@+id/details" 
                    android:layout_weight="1"
                    android:layout_width="0px" 
                    android:layout_height="match_parent"
                    android:background="?android:attr/detailsElementBackground" />
        </LinearLayout>

* 使用这个布局，activity一载入布局，系统就实例化TitlesFragment(它列出了戏剧标题)，尽管在屏幕右方的FrameLayout（其中的fragment用来显示即将列出的戏剧概要）消耗了内存，但是起先是空的。直到用户从列表中选择一项时，fragment才被置于FrameLayout中。

* 并不是所有的屏幕配置都足够宽，以显示并排的戏剧列表和摘要。所以，上面的布局仅适用于横向屏幕配置，且配置保存在res/layout-land/fragment_layout.xml。(-res/layout-land 横屏;-res/layout-port 竖屏;-res/layout 默认)

* 因此，当屏幕被置于纵向时，系统会适应下面的布局，它被保存在res/layout/fragment_layout.xml：

        <FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
            android:layout_width="match_parent" 
            android:layout_height="match_parent">

            <fragment class="com.example.android.apis.app.FragmentLayout$TitlesFragment"
                    android:id="@+id/titles"
                    android:layout_width="match_parent" 
                    android:layout_height="match_parent" />
        </FrameLayout>

* 这个布局只包含了TitlesFragment。这就意味着，当设备置于纵向时，只有戏剧标题列表是可见的。因此，当用户点击这个配置中的一个列表项时，应用程序将开启一个新的activity来显示摘要，而不是载入第二个fragment。

* 注意当用户点击一个列表项时会产生两个可能的行为：这依赖于两个布局哪个处于激活状态，它要么创建并显示一个新的fragment以在同一个activity中显示概要内容（添加fragment到FrameLayout），要么启动一个新的activity（在fragment可显示的地方）。

* TitlesFragment:

        public static class TitlesFragment extends ListFragment {
            boolean mDualPane;
            int mCurCheckPosition = 0;

            @Override
            public void onActivityCreated(Bundle savedInstanceState) {
                super.onActivityCreated(savedInstanceState);

                // Populate list with our static array of titles.
                setListAdapter(new ArrayAdapter<String>(getActivity(),
                        android.R.layout.simple_list_item_activated_1, Shakespeare.TITLES));

                // Check to see if we have a frame in which to embed the details
                // fragment directly in the containing UI.
                View detailsFrame = getActivity().findViewById(R.id.details);
                mDualPane = detailsFrame != null && detailsFrame.getVisibility() == View.VISIBLE;

                if (savedInstanceState != null) {
                    // Restore last state for checked position.
                    mCurCheckPosition = savedInstanceState.getInt("curChoice", 0);
                }

                if (mDualPane) {
                    // In dual-pane mode, the list view highlights the selected item.
                    getListView().setChoiceMode(ListView.CHOICE_MODE_SINGLE);
                    // Make sure our UI is in the correct state.
                    showDetails(mCurCheckPosition);
                }
            }

            @Override
            public void onSaveInstanceState(Bundle outState) {
                super.onSaveInstanceState(outState);
                outState.putInt("curChoice", mCurCheckPosition);
            }

            @Override
            public void onListItemClick(ListView l, View v, int position, long id) {
                showDetails(position);
            }

            /**
             * Helper function to show the details of a selected item, either by
             * displaying a fragment in-place in the current UI, or starting a
             * whole new activity in which it is displayed.
             */
            void showDetails(int index) {
                mCurCheckPosition = index;

                if (mDualPane) {
                    // We can display everything in-place with fragments, so update
                    // the list to highlight the selected item and show the data.
                    getListView().setItemChecked(index, true);

                    // Check what fragment is currently shown, replace if needed.
                    DetailsFragment details = (DetailsFragment)
                            getFragmentManager().findFragmentById(R.id.details);
                    if (details == null || details.getShownIndex() != index) {
                        // Make new fragment to show this selection.
                        details = DetailsFragment.newInstance(index);

                        // Execute a transaction, replacing any existing fragment
                        // with this one inside the frame.
                        FragmentTransaction ft = getFragmentManager().beginTransaction();
                        ft.replace(R.id.details, details);
                        ft.setTransition(FragmentTransaction.TRANSIT_FRAGMENT_FADE);
                        ft.commit();
                    }

                } else {
                    // Otherwise we need to launch a new activity to display
                    // the dialog fragment with selected text.
                    Intent intent = new Intent();
                    intent.setClass(getActivity(), DetailsActivity.class);
                    intent.putExtra("index", index);
                    startActivity(intent);
                }
            }
        }

* 第二个fragment，DetailsFragment在TitlesFragment的列表项被选中时显示戏剧概要。

        public static class DetailsFragment extends Fragment {
            /**
             * Create a new instance of DetailsFragment, initialized to
             * show the text at 'index'.
             */
            public static DetailsFragment newInstance(int index) {
                DetailsFragment f = new DetailsFragment();

                // Supply index input as an argument.
                Bundle args = new Bundle();
                args.putInt("index", index);
                f.setArguments(args);

                return f;
            }

            public int getShownIndex() {
                return getArguments().getInt("index", 0);
            }

            @Override
            public View onCreateView(LayoutInflater inflater, ViewGroup container,
                    Bundle savedInstanceState) {
                if (container == null) {
                    // We have different layouts, and in one of them this
                    // fragment's containing frame doesn't exist.  The fragment
                    // may still be created from its saved state, but there is
                    // no reason to try to create its view hierarchy because it
                    // won't be displayed.  Note this is not needed -- we could
                    // just run the code below, where we would create and return
                    // the view hierarchy; it would just never be used.
                    return null;
                }

                ScrollView scroller = new ScrollView(getActivity());
                TextView text = new TextView(getActivity());
                int padding = (int)TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP,
                        4, getActivity().getResources().getDisplayMetrics());
                text.setPadding(padding, padding, padding, padding);
                scroller.addView(text);
                text.setText(Shakespeare.DIALOGUE[getShownIndex()]);
                return scroller;
            }
        }   

* 从TitlesFragment类回想一下，如果用户点击列表项，并且目前的区域不包含R.id.details视图（这是DetailsFragment的所在），那么应用程序启动DetailsActivity以显示列表项内容。

* 下面是DetailsActivity，它只是在屏幕纵向时，嵌入DetailFragment以显示被选中的戏剧概要。

        public static class DetailsActivity extends Activity {

            @Override
            protected void onCreate(Bundle savedInstanceState) {
                super.onCreate(savedInstanceState);

                if (getResources().getConfiguration().orientation
                        == Configuration.ORIENTATION_LANDSCAPE) {
                    // If the screen is now in landscape mode, we can show the
                    // dialog in-line with the list so we don't need this activity.
                    finish();
                    return;
                }

                if (savedInstanceState == null) {
                    // During initial setup, plug in the details fragment.
                    DetailsFragment details = new DetailsFragment();
                    details.setArguments(getIntent().getExtras());
                    getFragmentManager().beginTransaction().add(android.R.id.content, details).commit();
                }
            }
        }     

---

> 参考文章：[Fragments](http://www.android-doc.com/guide/components/fragments.html)

---

### 结语：

坚持每天进步一点点...

---