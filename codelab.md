#CodeLab: Xây dựng ứng dụng chat với Firebase:
####Tải project android: [goo.gl/5xO5Rt](https://goo.gl/5xO5Rt)
<img src="https://codelabg.firebaseapp.com/assets/img/codelab/step_7.1.png" width="250">
<img src="https://codelabg.firebaseapp.com/assets/img/codelab/step_7.2.png" width="250">
####Demo Web: [codelabg.firebaseapp.com](https://codelabg.firebaseapp.com)
<img src="https://codelabg.firebaseapp.com/assets/img/codelab/step_0.1.png" width="400">
<br>
<br>
<br>
<br>
###Bước 1.1: Đăng ký
####[https://firebase.com](Firebase.com)
![](https://codelabg.firebaseapp.com/assets/img/codelab/step_0.png)

###Bước 1.2: Tạo project
![](https://codelabg.firebaseapp.com/assets/img/codelab/step_1.png)

####Thông tin:
1. Application Name: **NanoChat**
2. Company Domain: **firebase.gdg.com**
3. Project Location: **Desktop/**
4. Minimum SDK: **16 (Jelly Bean)**
5. Template: **Empty Activity**
6. Activity Name: **MainActivity**
7. LayoutName: **activity_main**
 
<br>
<br>
###Bước 2.1: Thêm thư viện
````Groovy
android {
    buildTypes {
        ...
    }
    packagingOptions {
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/LICENSE-FIREBASE.txt'
        exclude 'META-INF/NOTICE'
    }
}

dependencies {
    ...
    
    compile 'com.google.android.gms:play-services-auth:8.4.0'
    compile 'com.firebase:firebase-client-android:2.5.0'
    compile 'com.firebaseui:firebase-ui:0.3.1'
    
}
````
###Bước 2.2: Thêm INTERNET permission
````xml
<uses-permission android:name="android.permission.INTERNET"/>
````
###Bước 2.3: Tạo Firebase context.
###Bước 2.4: Thiết lập đường dẫn
````Java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    Firebase.setAndroidContext(this);
    mFirebaseRef = new Firebase("https://codelabg.firebaseio.com");
}
````

<br>
<br>
###Bước 3.1: Gửi tin nhắn: giao diện.
````xml
<LinearLayout
    android:id="@+id/footer"
    android:layout_alignParentBottom="true"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal">
    <EditText
        android:id="@+id/text_edit"
        android:layout_width="0dp"
        android:layout_weight="1"
        android:layout_height="wrap_content"
        android:singleLine="true"
        android:inputType="textShortMessage" />
    <Button
        android:id="@+id/send_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Send" />
</LinearLayout>
````
###Bước 3.2: Gửi tin nhắn: controller.
````Java
...
Firebase.setAndroidContext(this);
mFirebaseRef = new Firebase("https://codelabg.firebaseio.com");

final EditText textEdit = (EditText) this.findViewById(R.id.text_edit);
Button sendButton = (Button) this.findViewById(R.id.send_button);

sendButton.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        String text = textEdit.getText().toString();
        Map<String,Object> values = new HashMap<>();
        values.put("name", "Android User");
        values.put("text", text);
        mFirebaseRef.push().setValue(values);
        textEdit.setText("");
    }
});
````

<br>
<br>
###Bước 4.1: Thêm class ChatMessage
````Java
public class ChatMessage {
  private String name;
  private String text;
  
  public ChatMessage() {
      // dùng cho Firebase's deserializer
  }
}
````
###Bước 4.2: Thêm ListView.
````xml
...
tools:context="codelab.gdg.nanochat.MainActivity">

<ListView
    android:id="@android:id/list"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_above="@+id/footer"/>

<LinearLayout
    android:id="@+id/footer"
    ...
````
###Bước 4.3: Set Adapter
````Java
final ListView listView = (ListView) this.findViewById(android.R.id.list);
mListAdapter = new FirebaseListAdapter<ChatMessage>(this, ChatMessage.class,
        android.R.layout.two_line_list_item, mFirebaseRef) {
    @Override
    protected void populateView(View v, ChatMessage model, int position) {
        ((TextView) v.findViewById(android.R.id.text1)).setText(model.getName());
        ((TextView) v.findViewById(android.R.id.text2)).setText(model.getText());
    }
};
listView.setAdapter(mListAdapter);
````
###Bước 4.5: Đóng kết nối.
````Java
@Override
protected void onDestroy() {
    super.onDestroy();
    mListAdapter.cleanup();
}
````
<br>
<br>
###Bước 5.1: Bật Login bằng PASSWORD
<img src="https://codelabg.firebaseapp.com/assets/img/codelab/step_5.1.png">
###Bước 5.2: Login: giao diện
````xml
<ListView
  android:id="@android:id/list"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:layout_above="@+id/footer"/>

<Button
  android:layout_width="wrap_content"
  android:layout_height="wrap_content"
  android:text="Login"
  android:id="@+id/login"
  android:layout_alignTop="@android:id/list"
  android:layout_alignRight="@android:id/list"
  android:layout_alignEnd="@android:id/list" />

<LinearLayout
  android:id="@+id/footer"
````
###Bước 5.3: Sử dụng Login dialog
````Java
public class MainActivity extends FirebaseLoginBaseActivity {
````
###Bước 5.4: Cấu hình firebase cho login
````Java
@Override
protected Firebase getFirebaseRef() {
    return mFirebaseRef;
}
````
###Bước 5.5: Bật Login kiểu PASSWORD.
````Java
@Override
protected void onStart() {
    super.onStart();
    setEnabledAuthProvider(AuthProviderType.PASSWORD);
}
````
###Bước 5.6: Mở login dialog. 
````Java
Button loginButton = (Button) this.findViewById(R.id.login);
loginButton.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        showFirebaseLoginPrompt();
    }
});
````
###Bước 5.7: Hiển thị thông tin user
````Java
sendButton.setOnClickListener(new View.OnClickListener() {
  @Override
  public void onClick(View v) {
      String text = textEdit.getText().toString();
      ChatMessage message = new ChatMessage(mFirebaseRef.getAuth()
          .getProviderData().get("email").toString(), text);
      mFirebaseRef.push().setValue(message);
      textEdit.setText("");
  }
});
````
<br>
<br>
###Mở rộng: 
 - Logout
 - Login Facebook
 - Kết bạn, chat nhóm
  
 
<br>
<br>
<br>
<br>
###Hoàn thành

<img src="https://codelabg.firebaseapp.com/assets/img/codelab/step_7.1.png" width="250">
<img src="https://codelabg.firebaseapp.com/assets/img/codelab/step_7.2.png" width="250">
