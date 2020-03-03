---
layout: post
title: RecyclerView
description: RecyclerView 공부
date:   2020-03-03
categories: Kotlin
---

# [리사이클러뷰(RecyclerView)]

 [리사이클러뷰 개념](https://recipes4dev.tistory.com/154)



## Realm + RecyclerView(StaggerdGridLayoutManager) + 이벤트처리

1. adapter 만들기

2. 리사이클러뷰 LayoutManager 지정

3. 리사이클러뷰 adapter 지정

4. 소스코드

   - adapter 만들기 (이벤트처리 - onBindViewHolder()에서 viewholder객체에 이벤트리스너를 달아준다)

   ```kotlin
   package com.example.jmemo.adapter
   
   import android.content.Context
   import android.text.format.DateFormat
   import android.view.LayoutInflater
   import android.view.View
   import android.view.ViewGroup
   import android.widget.ImageView
   import android.widget.TextView
   import androidx.recyclerview.widget.RecyclerView
   import com.bumptech.glide.Glide
   import com.bumptech.glide.load.MultiTransformation
   import com.bumptech.glide.load.resource.bitmap.CenterCrop
   import com.bumptech.glide.load.resource.bitmap.RoundedCorners
   import com.bumptech.glide.request.RequestOptions
   import com.example.jmemo.R
   import com.example.jmemo.activity.EditActivity
   import com.example.jmemo.database.Memo
   import io.realm.OrderedRealmCollection
   import io.realm.RealmRecyclerViewAdapter
   import org.jetbrains.anko.startActivity
   
   
   class MemoGridRecyclerAdapter(realmResult: OrderedRealmCollection<Memo>, context: Context)
       : RealmRecyclerViewAdapter<Memo, MemoGridRecyclerAdapter.ViewHolderOfGridRecycleView>(realmResult, false){
       var context : Context? = null
       var realmResult : OrderedRealmCollection<Memo>? = null
       init {
           this.context = context
           this.realmResult = realmResult
       }
       override fun getItem(index: Int): Memo? {
           return super.getItem(index)
       }
   
       //아이템 뷰를 위한 뷰홀더 객체 생성하여 리턴
       override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolderOfGridRecycleView {
           val view = LayoutInflater.from(parent?.context).inflate(R.layout.item_memo_stagger_grid, parent, false)
           return ViewHolderOfGridRecycleView(view!!)
       }
       //position에 해당하는 데이터를 뷰홀더의 아이템뷰에 표시
       override fun onBindViewHolder(holder: ViewHolderOfGridRecycleView, position: Int) {
           val memo = realmResult?.get(position)
           holder.titleTextView.text = memo!!.title
           holder.dateTextView.text = DateFormat.format("yyyy년 MM월 dd일", memo.lastDate)
           holder.bodyTextView.text = memo.body
           if(memo.images.size != 0){
               holder.imageView.visibility = View.VISIBLE
               val multiOption = MultiTransformation(CenterCrop(), RoundedCorners(30))
               Glide.with(holder.view!!).load(memo.images.first())
                   .placeholder(R.drawable.ic_sync_black_24dp)
                   .error(R.drawable.ic_error)
                   .apply(RequestOptions.bitmapTransform(multiOption))
                   .into(holder.imageView)
               holder.imageView.background = holder.view!!.resources.getDrawable(R.drawable.border_layout, null)
           }
           else{
               holder.imageView.visibility = View.GONE
           }
           holder.view!!.setOnClickListener {
               context!!.startActivity<EditActivity>("id" to memo.id)
           }
       }
   
       class ViewHolderOfGridRecycleView(itemView: View): RecyclerView.ViewHolder(itemView){
           val titleTextView: TextView = itemView.findViewById(R.id.titleTextView)
           val dateTextView: TextView = itemView.findViewById(R.id.dateTextView)
           val bodyTextView: TextView = itemView.findViewById(R.id.bodyTextView)
           val imageView: ImageView = itemView.findViewById(R.id.mainImageView)
           val view = itemView
       }
   }
   ```

   - 리사이클러뷰 레이아웃매니저 지정, 어댑터 지정

   ```kotlin
   fun setViewStaggeredGridFromRealm(){
        val realmResult = realm.where<Memo>().findAll().sort("lastDate", Sort.DESCENDING)
        memoListRecyclerView.layoutManager = StaggeredGridLayoutManager(2, StaggeredGridLayoutManager.VERTICAL)
        val adapter = MemoGridRecyclerAdapter(realmResult, this)
        memoListRecyclerView.adapter = adapter
        realmResult.addChangeListener { _->
            val adapter = memoListRecyclerView.adapter
            adapter!!.notifyDataSetChanged()
        }
        //realmResult.addChangeListener { _-> adapter.notifyDataSetChanged() }
   }
   ```