#Google Map 개요

<a name="1"> </a>
## 1. Google Play Service 설정

* Google Play Services SDK 다운로드 및 설치
    - Android Studio에서 Tools>Android>SDK Manager 이용
        + **SDK Tools** 탭에서
        + **Google Play services** 선택 후 OK
     <img src="figures/google-play-service.png">
     
* 프로젝트에 라이브러리 추가
    1. build.gradle (Module:app) 파일 오픈
    2. 새로운 빌드 규칙 추가
    
		```java
		dependencies {
		      ...
		      compile 'com.google.android.gms:play-services-maps:11.0.4'
		}
		```
    3. 툴바에서 "Sync Project with Graddle Files(<img src="images/sync.png">)" 또는 Sync Now 클릭


.footnote[출처: https://developers.google.com/android/guides/setup]

---
<a name="2"> </a>
##2. Google Maps API 키 가져오기
1. Google Developers Console로 이동
	- **[+]** 버튼 클릭 --> 프로젝트 이름 입력 후, **[만들기]** 버튼 클릭
	<img src="figures/console_project.png">
	- **사용자 인증 정보 만들기** 클릭 후, **API 키** 선택
	- 생성된 API 키를 복사

---
<a name="3"></a>
##3. 앱에 지도 추가 및 설정
1. Manifest 파일에 API 키 추가
	    <application ...>
	        <meta-data
	            android:name="com.google.android.geo.API_KEY"
	            android:value="생성된 API 키를 여기로 붙여넣기" />
	         ...
	    </application>
	
	```
2. Activity에 지도를 위한 프레그먼트 추가
	<LinearLayout …>
        ...

3. 앱 실행시키기

	- 실제 Android 기기나 에뮬레이터에서 실행

		<img src="figures/google-map.png" width=200>

---
<a name="4"></a>
##4. 지도에 마커 추가 및 설정

[GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap) 객체를 사용할 수 있을 때 지도에 마커 및 기타 설정하기 위해서는 다음 절차를 진행

1. MainActivity에서 [OnMapReadyCallback](https://developers.google.com/android/reference/com/google/android/gms/maps/OnMapReadyCallback) 인터페이스를 구현

	``` java
	public class MainActivity extends AppCompatActivity implements 	OnMapReadyCallback {
		//...
	```

	```java
public class MainActivity extends AppCompatActivity implements OnMapReadyCallback{

	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        //...
	        SupportMapFragment mapFragment = (SupportMapFragment) getSupportFragmentManager()
	                .findFragmentById(R.id.map);
	        mapFragment.getMapAsync(this);
	    }    
    ```
	- 마커 추가 예제
	    public void onMapReady(GoogleMap googleMap) {
	        LatLng hansung = new LatLng(37.5817891, 127.008175);
	        googleMap.addMarker(new MarkerOptions().position(hansung).title("한성대학교"));
	        // move the camera
        	googleMap.moveCamera(CameraUpdateFactory.newLatLng(hansung));

	    }
	```
	
---
### 마커
- 지도에서 단일 위치 식별

	``` java
	public final Marker addMarker (MarkerOptions options)

	- [OnMarkerClickListener](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.OnMarkerClickListener)를 사용하여 마커에서 클릭 이벤트를 처리

```java
public class MainActivity extends AppCompatActivity implements OnMapReadyCallback{

    GoogleMap mGoogleMap = null;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        SupportMapFragment mapFragment = (SupportMapFragment) getSupportFragmentManager()
                .findFragmentById(R.id.map);
        mapFragment.getMapAsync(this);

        Button btn1 = (Button)findViewById(R.id.subway1);
        btn1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                if (mGoogleMap != null) {
                    LatLng location = new LatLng(37.5882827, 127.006390);
                    mGoogleMap.addMarker(
                            new MarkerOptions().
                                    position(location).
                                    title("한성대입구역").
                                    alpha(0.8f).
                                    icon(BitmapDescriptorFactory.fromResource(R.drawable.arrow)).
                                    snippet("4호선")
                    );
                    mGoogleMap.moveCamera(CameraUpdateFactory.newLatLngZoom(location,15));
                }
            }
        });

        Button btn2 = (Button)findViewById(R.id.subway2);
        btn2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                if (mGoogleMap != null) {
                    LatLng location2 = new LatLng(37.5793652, 127.015292);
                    mGoogleMap.addMarker(
                            new MarkerOptions().
                                    position(location2).
                                    title("창신역").
                                    alpha(0.8f).
                                    icon(BitmapDescriptorFactory.fromResource(R.drawable.arrow)).
                                    snippet("6호선")
                    );
                    mGoogleMap.moveCamera(CameraUpdateFactory.newLatLngZoom(location2,15));
                }
            }
        });

    }

    @Override
    public void onMapReady(GoogleMap googleMap) {
        mGoogleMap = googleMap;

        LatLng hansung = new LatLng(37.5817891, 127.009854);
        googleMap.addMarker(
                new MarkerOptions().
                        position(hansung).
                        title("한성대학교"));

        // move the camera
        googleMap.moveCamera(CameraUpdateFactory.newLatLngZoom(hansung,15));

        mGoogleMap.setOnMarkerClickListener(new MyMarkerClickListener());
    }

    class MyMarkerClickListener implements GoogleMap.OnMarkerClickListener {

        @Override
        public boolean onMarkerClick(Marker marker) {
            if (marker.getTitle().equals("한성대입구역")) {
                Toast.makeText(getApplicationContext(),"한성대입구역을 선택하셨습니다", Toast.LENGTH_SHORT).show();
            }
            return false;
        }
    }
}

```	

	
---
<a name="5"></a>
##5. 카메라 뷰 제어

###5.1 카메라 이동
- **카메라 위치를 변경**하여 지도에서 보이는 부분을 변경
	- [CameraUpdate](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate) CameraUpdateFactory.[zoomOut](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory.html#zoomOut())() : 한 단계 축소
	- [CameraUpdate](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate) CameraUpdateFactory.[zoomTo](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory.html#zoomTo(float))(float) : 지정된 단계로 줌 변경

	- [CameraUpdate](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate) CameraUpdateFactory.[newLatLngZoom](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory.html#newLatLngZoom(com.google.android.gms.maps.model.LatLng, float))(LatLng, float) : 지정된 위치로 지도의 중심을 이동하고,지정된 단계로 줌 변경
###5.2 카메라 뷰 업데이트

- [**GoogleMap** 클래스] public final void [moveCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#moveCamera(com.google.android.gms.maps.CameraUpdate)) (CameraUpdate update)
	- 주어진 **CameraUpdate** 객체로 즉시 카메라를 이동

- [**GoogleMap** 클래스] public final void [animateCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#animateCamera(com.google.android.gms.maps.CameraUpdate, int, com.google.android.gms.maps.GoogleMap.CancelableCallback)) (CameraUpdate update, int duration, GoogleMap.CancelableCallback callback)

```java
private static final LatLng SYDNEY = new LatLng(-33.88,151.21);