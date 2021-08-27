---
title: 안드로이드 GIS - GPS 정보를 RxJava로 처리하기
categories: [Android, GIS, RxJava]
tags: [android, gis, gps, location, rxjava]
---

# 1. 상황
안드로이드의 requestLocationUpdate()를 통해 GPS정보를 취득할 수 있고,  
callback으로 LocationListener를 해당 메소드의 파라미터로 주어 처리가 가능하다.  
그치만 나는 이미 Rx가 아니면 살 수 없는 몸이 되어버렸다.  

# 2. 생각
그냥 `LocationListener`에서 `PublishSubject<Location>`으로 데이터를 쏴주면 될 것 같다.

# 3. 코드
```kotlin
class LocationSource @Inject constructor( //Hilt로 주입했다.
    locationManager: LocationManager,
    criteria: Criteria,
    private val _currentLocation: PublishSubject<Location>
): LocationListener {
    val currentLocation: Observable<Location>
        get() =_currentLocation
        /*
            물론 LiveData를 활용해도 똑같이 구현이 가능할 것이다.
            다만 GPS데이터의 경우, 여러 스트림으로 나누어 throttle을 쓰기 좋아서
            우리 팀의 GIS 안드로이드 어플리케이션은 전부 RxJava를 활용한다.
        */
    init {
        val provider = locationManager.getBestProvider( //GPS_PROVIDER를 사용한다면 굳이 필요없다.
            criteria,
            true
        )
        locationManager.requestLocationUpdates(
            provider?: LocationManager.GPS_PROVIDER,
            GPS_MIN_TIME,
            GPS_MIN_DISTANCE,
            this
        )
    }

    override fun onLocationChanged(location: Location) {
        _currentLocation.onNext(location)
    }
}
```