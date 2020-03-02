---
layout: post
title: ViewPager
description: ViewPager 공부
date:   2020-03-02
categories: Kotlin
---
# [ViewPager]

## 개념

1. 뷰페이저는 여러 프래그먼트들을 좌우로 슬라이드 하는 뷰
2. 뷰페이저를 사용하려면 데이터, 어댑터, 뷰 세 가지가 필요한 어댑터 패턴을 구현해야 함
3. 세가지 개념에 해당하는 것은 각각 다음과 같다.
   - 데이터 : 프래그먼트 (화면)
   - 어댑터 : 프래그먼트를 어느 화면에 표시할 것인지 관리하는 객체
   - 뷰 : 뷰페이저

## 어댑터 작성

1. ViewPager에 표시할 내용을 정의하려면 어댑터가 필요
2. 어댑터는 아이템의 목록 정보를 가진 객체
3. 프래그먼트를 아이템으로 가지면서 ViewPager에 설정하는 어댑터는 다음 두 가지가 있다.
   - FragmentPagerAdapter : 페이지 내용이 영구적일 때 적합, 한번 로딩한 페이지는 메모리에 보관하기 때문에 빠르다. 그래서 페이지가 많으면 많은 메모리를 사용
   - FragmentStatePagerAdapter : 많은 수의 페이지가 있을 때 적합하다. 보이지 않는 페이지를 메모리에서 제거할 수 있다. 상대적으로 적은 메모리를 차지한다.
4. 소스코드

```kotlin
package com.example.mygallery

import androidx.fragment.app.Fragment
import androidx.fragment.app.FragmentManager
import androidx.fragment.app.FragmentStatePagerAdapter

class MyPagerAdapter(fm: FragmentManager, behavior: Int) : FragmentStatePagerAdapter(fm, behavior) {
    //뷰페이저가 표시할 프래그먼트 목록
    private val items = ArrayList<Fragment>()
    //position위치의 프래그먼트
    override fun getItem(position: Int): Fragment {
        return items[position]
    }
    //아이템 개수
    override fun getCount(): Int {
        return items.size
    }
    //아이템 갱신
    fun updateFragments(items: List<Fragment>){
        this.items.addAll(items)
    }
}
```



## 액티비티에서의 코드

1. 프래그먼트를 아이템으로 하는 ArrayList를 생성, 사진을 Cursor 객체로부터 가져올 때마다 PhotoFragment.newInstance(uri)로 프래그먼트를 생성하면서 fragments리스트에 추가한다.
2. MyPagerAdapter를 생성하면서 프래그먼트 매니저를 생성자의 인자로 전달해야 함. 프래그먼트 매니저는 getSupportFragmentManager() 메서드로 가져올 수 있고 코틀린에서는 supportFragmentManager 프로퍼티로 접근할 수 있음. 어댑터를 생성하면 updateFragments()메서드를 사용하여 프래그먼트 리스트를 전달한다. 어댑터를 viewPager에 설정한다.

```kotlin
private fun getAllPhotos(){
    //모든 사진 정보 가져오기
    val cursor = contentResolver.query(MediaStore.Images.Media.EXTERNAL_CONTENT_URI,
            null, // 가져올 항목 배열, 모르겠으면 null -> 모든항목을 가져옴
            null,  // 조건
            null, // 조건
            MediaStore.Images.ImageColumns.DATE_TAKEN + " DESC")
    //1번
    val fragments = ArrayList<Fragment>()
    if(cursor != null){
        val uriExternal: Uri = MediaStore.Images.Media.EXTERNAL_CONTENT_URI
        while(cursor.moveToNext()){
            //사진 경로 Uri 가져오기
            //val columnIndexId = cursor.getColumnIndexOrThrow(MediaStore.Images.Media._ID)
            //val imageId = cursor.getLong(columnIndexId)
            //val uriImage = Uri.withAppendedPath(uriExternal, "" + imageId)
            //Log.d("MainActivity", uriImage.toString())
            val uri = cursor.getString(cursor.getColumnIndexOrThrow(MediaStore.Images.Media.DATA))
            Log.d("MainActivity", uri)
            fragments.add(PhotoFragment.newInstance(uri))
        }
        cursor.close()
    }
    //2번
    val adapter = MyPagerAdapter(supportFragmentManager, FragmentStatePagerAdapter.BEHAVIOR_RESUME_ONLY_CURRENT_FRAGMENT)
    adapter.updateFragments(fragments)
    viewPager.adapter = adapter

    timer(period = 3000){
        runOnUiThread {
            if(viewPager.currentItem < adapter.count - 1){
                viewPager.currentItem = viewPager.currentItem + 1
            }
            else{
                viewPager.currentItem = 0
            }
        }
    }
}
```

