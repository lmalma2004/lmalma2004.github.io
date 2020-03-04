---
layout: post
title: anko 라이브러리를 통한 액티비티 정보전달, SharedPreference 예제
description: anko, SharedPreference
date:   2020-03-04
categories: Kotlin
---
# anko 라이브러리를 통한 액티비티 정보전달

1. mainActivity에서 실행 코드

```kotlin
startActivity<ResultActivity>(
    "weight" to weightEditText.text.toString(),
    "height" to heightEditText.text.toString()
)
```

2. ResultActivity에서의 코드

```kotlin
val height = intent.getStringExtra("height").toInt()
val weight = intent.getStringExtra("weight").toInt()
```

# SharedPreference로 데이터 저장하기



데이터 저장, 불러오기 예제

```kotlin
private fun saveData(height: Int, weight: Int){
    //프리퍼런스 매니저를 사용해 프리퍼런스 객체를 얻는다
    val pref = androidx.preference.PreferenceManager.getDefaultSharedPreferences(this)
    //프리퍼런스 객체의 에디터 객체를 얻는다. 이 객체를 사용해 프리퍼런스에 데이터를 담을 수 있다.
    val editor = pref.edit()
    //에디터 객체에 put[데이터 타입] 형식의 메서드를 사용하여 키와 값 형태의 쌍으로 저장을 한다
    //put 메서드는 기본 타입은 모두 지원한다.
    editor.putInt("KEY_HEIGHT", height).putInt("KEY_WEIGHT", weight).apply()

}

private fun loadData(){
    val pref = androidx.preference.PreferenceManager.getDefaultSharedPreferences(this)
    val height = pref.getInt("KEY_HEIGHT", 0)
    val weight = pref.getInt("KEY_WEIGHT", 0)

    if(height != 0 && weight != 0){
        heightEditText.setText(height.toString())
        weightEditText.setText(weight.toString())
    }
}
```