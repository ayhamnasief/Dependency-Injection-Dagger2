# Dependency Injection - Dagger 2

![image](https://github.com/user-attachments/assets/2a8bb251-0b50-4a47-9306-35f050a9246e)

#### Dependencies
```java
implementation("com.google.dagger:dagger-android:2.20")
annotationProcessor("com.google.dagger:dagger-android-processor:2.20")
annotationProcessor("com.google.dagger:dagger-compiler:2.20")
```

#### Constructor Injection
```java
public class A {
    @Inject
    public A() {
    }
}
------------------------------------------------------------------------  
public class B {
    @Inject
    public B() {
    }
}
------------------------------------------------------------------------ 
public class C {
    @Inject
    public C(A a, B b) {
        System.out.println(a.toString() + " - " + b.toString());
    }
}
------------------------------------------------------------------------ 
@Component
public interface ControlComponent {
C getC();
}
------------------------------------------------------------------------ 
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        ControlComponent controlComponent = DaggerControlComponent.create();
        controlComponent.getC();
    }
```

#### Method & Field Injection

*Condition:*
- Fields can't be private or final
- You should make inject constructor for all fileds classes

```java
public class A {
    @Inject
    public A() {
    }
}
------------------------------------------------------------------------  
public class B {
    @Inject
    public B() {
    }
}

------------------------------------------------------------------------ 
public class C {
    @Inject
    A a;
    @Inject
    B b;
    @Inject
    public C() {
    System.out.println(a.toString() + " + " + b.toString()); // Give Error
    }

    void printC(){
        System.out.println(a.toString() + " + " + b.toString());
    }
}
------------------------------------------------------------------------ 
@Component
public interface CComponent {
    C getC();
}
------------------------------------------------------------------------ 
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        CComponent component = DaggerCComponent.create();
        component.getC().printC();
    }
}
```

#### Inject Main

```java
EX1:

public class Test {

    @Inject
    public Test(){}

    public String print(){return "Class Test";}
}
-------------------------------------------------

@Component
public interface ControlComponent {
    public void inject(MainActivity mainActivity);
}
---------------------------------------------------

public class MainActivity extends AppCompatActivity {


    @Inject
    Test test;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        setContentView(R.layout.activity_main);

        ControlComponent controlComponent = DaggerControlComponent.create();
        controlComponent.inject(this);

        Log.d("ayham", test.print()+"");
    }
}
```

```java
EX2:

public class A {
    @Inject
    public A() {
    }
}
------------------------------------------------------------------------  
public class B {
    @Inject
    public B() {
    }
}
------------------------------------------------------------------------ 
public class C {
    @Inject
    A a;
    @Inject
    B b;
    @Inject
    public C() {
    }

    void print() {
        System.out.println(a.toString() + " - " + b.toString());
    }
}
------------------------------------------------------------------------ 
@Component
public interface ControlComponent {
void inject(MainActivity mainActivity);
}
------------------------------------------------------------------------ 
public class MainActivity extends AppCompatActivity {
    @Inject
    C c;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        ControlComponent controlComponent = DaggerControlComponent.create();
        controlComponent.inject(this);
        c.print();
    }
}
```

![image](https://github.com/user-attachments/assets/6fca8d8c-7147-4840-8366-e7b1c0860d3a)

#### @Module & @Prov

```java
public class A {
    @Inject
    public A() {
    Log.d("ayham", "Class A");
    }
}
------------------------------------------------------------------------
public class B {
    public B() {
        Log.d("ayham", "Class B");
    }
}
------------------------------------------------------------------------
public class C {
    @Inject
    A a;
    B b;

    @Inject
    public C(B b) {
        this.b = b;
        Log.d("ayham", "Class C");
    }
    void print(){
        Log.d("ayham", a.toString() + " And " + b.toString());

    }
}
------------------------------------------------------------------------
@Module
public class ControlModule {
    @Provides
    B provideB(){
        return new B();
    }
}
------------------------------------------------------------------------
@Component(modules = ControlModule.class)
public interface ControlComponent {
    C getC();
    void inject(MainActivity mainActivity);
}
------------------------------------------------------------------------
public class MainActivity extends AppCompatActivity {
    @Inject
    C c;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ControlComponent controlComponent = DaggerControlComponent.create();
        controlComponent.inject(this);
        c.print();
    }
}
```

#### Run time dependency injection

```java
EX1:
public class A {
    public A() {
    Log.d("ayham", "Class A");
    }
}
------------------------------------------------------------------------
public class B {
    public B() {
        Log.d("ayham", "Class B");
    }
}
------------------------------------------------------------------------
public class C {
    A a;
    B b;
    int intVal;

    @Inject
    public C(A a, B b, int intVal) {
        this.a = a;
        this.b = b;
        this.intVal = intVal;
        Log.d("ayham", "Class C");
    }
    void print(){
        Log.d("ayham", a.toString() + " And " + b.toString() + "intVal: " + intVal);
    }
}
------------------------------------------------------------------------
@Module
public class ControlModule {

    @Provides
    A prvideA(){
        return new A();
    }
    @Provides
    B provideB(){
        return new B();
    }
    @Provides
    int getIntVal(){
        return 3;
    }
}
------------------------------------------------------------------------
@Component(modules = ControlModule.class)
public interface ControlComponent {
    C getC();
    void inject(MainActivity mainActivity);
}
------------------------------------------------------------------------
public class MainActivity extends AppCompatActivity {
    @Inject
    C c;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ControlComponent controlComponent = DaggerControlComponent.create();
        controlComponent.inject(this);
        c.print();
    }
}
```
```java
EX2:

public class A {
    public A() {
    Log.d("ayham", "Class A");
    }
}
------------------------------------------------------------------------
public class B {
    public B() {
        Log.d("ayham", "Class B");
    }
}
------------------------------------------------------------------------
public class C {
    A a;
    B b;
    int intVal;

    @Inject
    public C(A a, B b, int intVal) {
        this.a = a;
        this.b = b;
        this.intVal = intVal;
        Log.d("ayham", "Class C");
    }
    void print(){
        Log.d("ayham", a.toString() + " And " + b.toString() + "intVal: " + intVal);
    }
}
------------------------------------------------------------------------
@Module
public class ControlModule {
int intVal;

    public ControlModule(int intVal) {
        this.intVal = intVal;
    }
    @Provides
    A prvideA(){
        return new A();
    }
    @Provides
    B provideB(){
        return new B();
    }
    @Provides
    int getIntVal(){
        return intVal;
    }
}
------------------------------------------------------------------------
@Component(modules = ControlModule.class)
public interface ControlComponent {
    C getC();
    void inject(MainActivity mainActivity);
}
------------------------------------------------------------------------
public class MainActivity extends AppCompatActivity {
    @Inject
    C c;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ControlComponent controlComponent = DaggerControlComponent.builder().controlModule(new ControlModule(5)).build();
        controlComponent.inject(this);
        c.print();
    }
}
```

#### Component builder & qualifiers

```java
EX1:

public class A {
    public A() {
    Log.d("ayham", "Class A");
    }
}
------------------------------------------------------------------------
public class B {
    public B() {
        Log.d("ayham", "Class B");
    }
}
------------------------------------------------------------------------
public class C {
    A a;
    B b;
    int intVal1;

    @Inject
    public C(A a, B b, int intVal1) {
        this.a = a;
        this.b = b;
        this.intVal1 = intVal1;
        Log.d("ayham", "Class C");
    }
    void print(){
        Log.d("ayham", a.toString() + " And " + b.toString() + "intVal1: " + intVal1);
    }
}
------------------------------------------------------------------------
@Module
public class ControlModule {
    @Provides
    A prvideA(){
        return new A();
    }
    @Provides
    B provideB(){
        return new B();
    }
}
------------------------------------------------------------------------
@Component(modules = ControlModule.class)
public interface ControlComponent {
    C getC();
    void inject(MainActivity mainActivity);

    @Component.Builder
    interface Builder{
        @BindsInstance
        Builder intVal(int val);
        ControlComponent build();
    }
}
------------------------------------------------------------------------
public class MainActivity extends AppCompatActivity {
    @Inject
    C c;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ControlComponent controlComponent = DaggerControlComponent.builder().intVal(6).build();
        controlComponent.inject(this);
        c.print();
    }
}
```
```java
EX2:
public class A {
    public A() {
    Log.d("ayham", "Class A");
    }
}
------------------------------------------------------------------------
public class B {
    public B() {
        Log.d("ayham", "Class B");
    }
}
------------------------------------------------------------------------
public class C {
    A a;
    B b;
    int intVal1;
    int intVal2;

    @Inject
    public C(A a, B b, int intVal1, int intVal2) {
        this.a = a;
        this.b = b;
        this.intVal1 = intVal1;
        this.intVal2 = intVal2;
        Log.d("ayham", "Class C");
    }
    void print(){
        Log.d("ayham", a.toString() + " And " + b.toString() + "intVal1: " + intVal1 + " - intVal2: " + intVal2);
    }
}
------------------------------------------------------------------------
@Module
public class ControlModule {
    @Provides
    A prvideA(){
        return new A();
    }
    @Provides
    B provideB(){
        return new B();
    }
}
------------------------------------------------------------------------
@Component(modules = ControlModule.class)
public interface ControlComponent {
    C getC();
    void inject(MainActivity mainActivity);

    @Component.Builder
    interface Builder{
        @BindsInstance
        Builder intVal(int val);
        ControlComponent build();
    }
}
------------------------------------------------------------------------
public class MainActivity extends AppCompatActivity {
    @Inject
    C c;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ControlComponent controlComponent = DaggerControlComponent.builder().intVal(6).build();
        controlComponent.inject(this);
        c.print();
    }
}
```
```java
EX3:
public class A {
    public A() {
    Log.d("ayham", "Class A");
    }
}
------------------------------------------------------------------------
public class B {
    public B() {
        Log.d("ayham", "Class B");
    }
}
------------------------------------------------------------------------
public class C {
    A a;
    B b;
    int intVal1;
    int intVal2;

    @Inject
    public C(A a, B b, @Named("val1") int intVal1, @Named("val2") int intVal2) {
        this.a = a;
        this.b = b;
        this.intVal1 = intVal1;
        this.intVal2 = intVal2;
        Log.d("ayham", "Class C");
    }
    void print(){
        Log.d("ayham", a.toString() + " And " + b.toString() + "intVal1: " + intVal1 + " - intVal2: " + intVal2);
    }
}
------------------------------------------------------------------------
@Module
public class ControlModule {
    @Provides
    A prvideA(){
        return new A();
    }
    @Provides
    B provideB(){
        return new B();
    }
}
------------------------------------------------------------------------
@Component(modules = ControlModule.class)
public interface ControlComponent {
    C getC();
    void inject(MainActivity mainActivity);

    @Component.Builder
    interface Builder{
        @BindsInstance
        Builder intVal1(@Named("val1") int val);
        @BindsInstance
        Builder intVal2(@Named("val2") int val);
        ControlComponent build();
    }
}
------------------------------------------------------------------------
public class MainActivity extends AppCompatActivity {
    @Inject
    C c;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ControlComponent controlComponent = DaggerControlComponent.builder().intVal1(6).intVal2(4).build();
        controlComponent.inject(this);
        c.print();
    }
}
```
```java
EX4:
@Qualifier
@Documented
@Retention(RUNTIME)
public @interface IntVal1 {
    String value() default "intVal1";
}

@Qualifier
@Documented
@Retention(RUNTIME)
public @interface IntVal2 {
    String value() default "intVal2";
}
-----------------------------------------------------------------------

public class A {
    public A() {
    Log.d("ayham", "Class A");
    }
}
------------------------------------------------------------------------
public class B {
    public B() {
        Log.d("ayham", "Class B");
    }
}
}
------------------------------------------------------------------------

public class C {
    A a;
    B b;
    int intVal1;
    int intVal2;

    @Inject
    public C(A a, B b, @IntVal1 int intVal1, @IntVal2 int intVal2) {
        this.a = a;
        this.b = b;
        this.intVal1 = intVal1;
        this.intVal2 = intVal2;
        Log.d("ayham", "Class C");
    }
    void print(){
        Log.d("ayham", a.toString() + " And " + b.toString() + "intVal1: " + intVal1 + " - intVal2: " + intVal2);
    }
}
------------------------------------------------------------------------
@Module
public class ControlModule {
    @Provides
    A prvideA(){
        return new A();
    }
    @Provides
    B provideB(){
        return new B();
    }
}
------------------------------------------------------------------------
@Component(modules = ControlModule.class)
public interface ControlComponent {
    C getC();
    void inject(MainActivity mainActivity);

    @Component.Builder
    interface Builder{
        @BindsInstance
        Builder intVal1(@IntVal1 int val);
        @BindsInstance
        Builder intVal2(@IntVal2 int val);
        ControlComponent build();
    }
}
------------------------------------------------------------------------
import dagger.Module;
import dagger.Provides;

@Module
public class CoffeeModule {
    
    @Provides
    River provideRiver(){
        return new River();
    }

}
------------------------------------------------------------------------
public class MainActivity extends AppCompatActivity {
    @Inject
    C c;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ControlComponent controlComponent = DaggerControlComponent.builder().intVal1(6).intVal2(4).build();
        controlComponent.inject(this);
        c.print();
    }
```

#### General Scope & @Singleton

```java
EX1:
public class A {
    public A() {
    Log.d("ayham", "Class A");
    }
}
------------------------------------------------------------------------

public class B {
    public B() {
        Log.d("ayham", "Class B");
    }
}
------------------------------------------------------------------------

@Singleton
public class C {
    A a;
    @Inject
    B b;
    int intVal1;
    int intVal2;

    @Inject
    public C(A a, @IntVal1 int intVal1, @IntVal2 int intVal2) {
        this.a = a;
        this.intVal1 = intVal1;
        this.intVal2 = intVal2;
        Log.d("ayham", "Class C");
    }
    void print(){
        Log.d("ayham", a.toString() + " And " + b.toString() + "intVal1: " + intVal1 + " - intVal2: " + intVal2);
    }
}
------------------------------------------------------------------------
public class MainApplication extends Application {
    private ControlComponent controlComponent;
    @Override
    public void onCreate() {
        super.onCreate();
        controlComponent = DaggerControlComponent.builder().intVal1(6).intVal2(4).build();
    }

    public ControlComponent getControlComponent() {
        return controlComponent;
    }
}
------------------------------------------------------------------------
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:name=".MainApplication"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.Dagger2_JAVA"
        tools:targetApi="31">
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
------------------------------------------------------------------------
@Module
public class ControlModule {
    @Provides
    A prvideA(){
        return new A();
    }
    @Provides
    B provideB(){
        return new B();
    }
}
------------------------------------------------------------------------
@Singleton
@Component(modules = ControlModule.class)
public interface ControlComponent {
    C getC();
    void inject(MainActivity mainActivity);

    @Component.Builder
    interface Builder{
        @BindsInstance
        Builder intVal1(@IntVal1 int val);
        @BindsInstance
        Builder intVal2(@IntVal2 int val);
        ControlComponent build();
    }
}
------------------------------------------------------------------------
public class MainActivity extends AppCompatActivity {
    @Inject
    C c1,c2;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ControlComponent controlComponent = ((MainApplication)getApplication()).getControlComponent();
        controlComponent.inject(this);
        c1.print();
        Log.d("ayham","C1 REF: "+c1);
        c2.print();
        Log.d("ayham","C2 REF: "+c2);
    }
}
```

```java
EX2:
public class A {
    public A() {
    Log.d("ayham", "Class A");
    }
}
------------------------------------------------------------------------
@Singleton
public class B {
    @Inject
    public B() {
        Log.d("ayham", "Class B");
    }
}
------------------------------------------------------------------------
@Singleton
public class C {
    A a;
    @Inject
    B b;
    int intVal1;
    int intVal2;

    @Inject
    public C(A a, @IntVal1 int intVal1, @IntVal2 int intVal2) {
        this.a = a;
        this.intVal1 = intVal1;
        this.intVal2 = intVal2;
        Log.d("ayham", "Class C");
    }
    void print(){
        Log.d("ayham", a.toString() + " And " + b.toString() + " - intVal1: " + intVal1 + " - intVal2: " + intVal2);
    }
}
------------------------------------------------------------------------
public class MainApplication extends Application {
    private ControlComponent controlComponent;
    @Override
    public void onCreate() {
        super.onCreate();
        controlComponent = DaggerControlComponent.builder().intVal1(6).intVal2(4).build();
    }

    public ControlComponent getControlComponent() {
        return controlComponent;
    }
}
------------------------------------------------------------------------
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:name=".MainApplication"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.Dagger2_JAVA"
        tools:targetApi="31">
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
------------------------------------------------------------------------
@Module
public class ControlModule {
    @Provides
    A prvideA(){
        return new A();
    }
    @Provides
    B provideB(){
        return new B();
    }
}
------------------------------------------------------------------------
@Singleton
@Component(modules = ControlModule.class)
public interface ControlComponent {
    C getC();
    void inject(MainActivity mainActivity);

    @Component.Builder
    interface Builder{
        @BindsInstance
        Builder intVal1(@IntVal1 int val);
        @BindsInstance
        Builder intVal2(@IntVal2 int val);
        ControlComponent build();
    }
}
------------------------------------------------------------------------
public class MainActivity extends AppCompatActivity {
    @Inject
    C c1,c2;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ControlComponent controlComponent = ((MainApplication)getApplication()).getControlComponent();
        controlComponent.inject(this);
        c1.print();
        Log.d("ayham","C1 REF: "+c1 + " - B REF: "+c1.a);
        c2.print();
        Log.d("ayham","C2 REF: "+c2 + " - B REF: "+c2.a);
    }
}
```

