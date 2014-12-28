---
layout: post
category: Android
title: Tabhost enable onResume
tagline: by hombo
tags: [android]
---

我们知道tabhost包含的子activiy的生命周期和正常的activity的生命周期不太一样，tabhost中的activiy屏蔽掉了onResume()，onPause()等方法，那怎么让这些方法去除屏蔽呢，下面的例子，显示了使能onResume和onPause的方式。

<!--more-->

####重点是利用LocalActivityManager
在tabhost的主activity中建立一个LocalActivityManager变量，然后重写onResume()，onPause()两个方法：

	@Override
    protected void onResume() {
		super.onResume();
		mLocalActivityManager.dispatchResume();
    }

    @Override
    protected void onPause() {
    	super.onPause();
    	mLocalActivityManager.dispatchPause(isFinishing());
    }
    
在onResume中调用LocalActiviManager的dispatchResume()方法，在onPause中调用LocalActiviManager的dispatchPause()方法，然后在切换tab时，调用

	mLocalActivityManager.dispatchPause(isFinishing());
	mLocalActivityManager.dispatchResume();

这样就可以让子activiy的onResume()，onPause()使能。



具体实例代码如下：

	//  tabhost enable onResume and onPause
	 
	package com.example.ikteacher.activity;
	
	import com.example.ikteacher.R;
	import com.example.ikteacher.R.anim;
	import com.example.ikteacher.R.id;
	import com.example.ikteacher.R.layout;
	
	import android.app.Activity;
	import android.app.LocalActivityManager;
	import android.app.TabActivity;
	import android.content.Intent;
	import android.os.Bundle;
	import android.view.View;
	import android.widget.Button;
	import android.widget.ImageButton;
	import android.widget.TabHost;
	
	public class HomeInteractActivity extends TabActivity {
	
		TabHost tabHost;
	
		ImageButton btn_tab_message, btn_tab_contacts;
		ImageButton btn_title_back, btn_tile_add;
		LocalActivityManager mLocalActivityManager;
	
		@SuppressWarnings("deprecation")
		@Override
		protected void onCreate(Bundle savedInstanceState) {
			// TODO Auto-generated method stub
			super.onCreate(savedInstanceState);
			setContentView(R.layout.activity_home_interact);
			
			mLocalActivityManager = new LocalActivityManager(this, false);
	
			btn_title_back = (ImageButton) findViewById(R.id.btn_title_back);
			btn_title_back.setOnClickListener(new Button.OnClickListener() {
	
				@Override
				public void onClick(View v) {
					// TODO Auto-generated method stub
					HomeInteractActivity.this.finish();
				}
			});
	
			btn_tab_message = (ImageButton) findViewById(R.id.btn_tab_message);
			btn_tab_contacts = (ImageButton) findViewById(R.id.btn_tab_contacts);
	
			btn_tab_message.setSelected(true);
			
			tabHost = getTabHost();
			tabHost.addTab(tabHost.newTabSpec("message").setIndicator("Message")
					.setContent(new Intent(this, MessageActivity.class).addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP)));
			tabHost.addTab(tabHost.newTabSpec("contacts").setIndicator("Contacts")
					.setContent(new Intent(this, ContactsActivity.class)));
	
			tabHost.setCurrentTabByTag("message");
		}
	
		/**
		 * tab点击事件
		 * 
		 * @param view
		 */
		public void onTabClicked(View view) {
			switch (view.getId()) {
			case R.id.btn_tab_message:
				btn_tab_contacts.setSelected(false);
				btn_tab_message.setSelected(true);
				tabHost.setCurrentTabByTag("message");
				mLocalActivityManager.dispatchPause(isFinishing());
				mLocalActivityManager.dispatchResume();
				break;
			case R.id.btn_tab_contacts:
				btn_tab_message.setSelected(false);
				btn_tab_contacts.setSelected(true);
				tabHost.setCurrentTabByTag("contacts");
				mLocalActivityManager.dispatchPause(isFinishing());
				mLocalActivityManager.dispatchResume();
				break;
			}
		}
	
		@Override
		public void finish() {
			// TODO Auto-generated method stub
			super.finish();
			this.overridePendingTransition(R.anim.in_from_left, R.anim.out_to_right);
		}
		
		@SuppressWarnings("deprecation")
		@Override
	    protected void onResume() {
			super.onResume();
			mLocalActivityManager.dispatchResume();
	    }
	
	    @Override
	    protected void onPause() {
	    	super.onPause();
	    	mLocalActivityManager.dispatchPause(isFinishing());
	    }
	}







