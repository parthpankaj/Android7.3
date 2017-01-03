<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">
    <LinearLayout
        android:id="@+id/ll_for_display"
        android:orientation="horizontal"
        android:weightSum="4"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
        <LinearLayout
            android:id="@+id/ll_for_tetxview"
            android:orientation="vertical"
            android:background="#a4cfec"
            android:layout_weight="1"
            android:weightSum="3"

            android:layout_width="wrap_content"
            android:layout_height="match_parent">
            <TextView
                android:id="@+id/tv_name"
                android:layout_weight="1"
                android:textSize="16sp"
                android:textStyle="bold"
                android:textAlignment="textEnd"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:text="@string/name" />

            <TextView
                android:id="@+id/tv_age"
                android:layout_weight="1"
                android:background="#a4cfec"
                android:textSize="16sp"
                android:textStyle="bold"
                android:textAlignment="textEnd"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:text="@string/age"/>
            <TextView
                android:id="@+id/tv_sex"
                android:layout_weight="1"
                android:background="#a4cfec"
                android:textSize="16sp"
                android:textStyle="bold"
                android:textAlignment="textEnd"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:text="@string/sex"/>

        </LinearLayout>



        <LinearLayout
            android:id="@+id/ll_for_editview"
            android:orientation="vertical"
            android:layout_weight="3"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content">

            <EditText
                android:id="@+id/ev_name"
                android:textColor="#262424"
                android:hint="@string/name"
                android:layout_weight="1"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content" />

            <EditText
                android:id="@+id/ev_age"
                android:hint="@string/age"
                android:textColor="#262424"
                android:layout_weight="1"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content" />
            <EditText
                android:id="@+id/ev_sex"
                android:hint="@string/sex"
                android:textColor="#262424"
                android:layout_weight="1"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content" />

        </LinearLayout>

    </LinearLayout>

    <RelativeLayout
        android:layout_below="@id/ll_for_display"
        android:id="@+id/ll_image_label"
        android:background="#a4cfec"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="10dp"
        android:orientation="horizontal">
        <TextView
            android:id="@+id/tv_image"
            android:layout_centerHorizontal="true"
            android:layout_centerVertical="true"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#ff0000"
            android:textSize="16sp"
            android:textStyle="bold"
            android:text="@string/employee_photo" />

    </RelativeLayout>

    <RelativeLayout
        android:layout_below="@id/ll_for_display"
        android:id="@+id/ll_image_display"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">
        <ImageView
            android:id="@+id/iv_user_photo"
            android:layout_width="200dp"
            android:layout_height="200dp"
            android:src="@mipmap/ic_launcher"
            android:layout_centerHorizontal="true"
            android:layout_centerVertical="true"/>

    </RelativeLayout>


</RelativeLayout>
---------
package com.example.pankaj.storage73;

import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.os.Bundle;
import android.os.Handler;
import android.support.v7.app.AppCompatActivity;
import android.util.Log;
import android.widget.EditText;
import android.widget.ImageView;

import java.io.ByteArrayOutputStream;
import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {

    EditText editText1, editText2, editText3;
    ImageView imageView;
    Employee employee;
    private DBHelper dbHelper;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        imageView = (ImageView)findViewById(R.id.iv_user_photo);
        editText1 = (EditText) findViewById(R.id.ev_name);
        editText2 = (EditText) findViewById(R.id.ev_age);
        editText3 = (EditText) findViewById(R.id.ev_sex);

        Log.d("Insert: ", "Inserting ..");
        // Saving to Database...
        dbHelper = new DBHelper(this);
        if(dbHelper.numberOfRows()>0){
            Log.e("MA ","Database already exist.");
        }
        else{
            saveImageInDB();
            Log.e("MA ","Image Saved in Database.");

        }

        //blob to image conversion
        new Handler().postDelayed(new Runnable() {
            @Override
            public void run() {
                if (loadImageFromDB()) {
                    Log.e("MA ","Image Loaded from Database.");
                }
            }
        }, 0);

    }

    Boolean saveImageInDB() {
        employee = new Employee();
        employee.setEmployeeName("ASHISH KUMAR");
        employee.setEmployeeAge("34");
        employee.setEmployeeSex("MALE");

        Bitmap bitmapImage = BitmapFactory.decodeResource(getResources(),R.drawable.userimage);
        // convert bitmap to byte
        ByteArrayOutputStream stream = new ByteArrayOutputStream();
        bitmapImage.compress(Bitmap.CompressFormat.PNG, 100, stream);
        byte imageInByte[] = stream.toByteArray();
        employee.employeeimageInByte = imageInByte;


        Log.d("Insert: ", "Inserting ..");
        dbHelper.insertEmployee(employee);
        return true;

    }

    Boolean loadImageFromDB() {
        try {
            ArrayList array_list = dbHelper.getAllEmployee();
            Log.e("Employee Size ", String.valueOf(array_list.size()));

            if(!array_list.isEmpty()){
                Employee employee1 = (Employee) array_list.get(0);

                editText1.setText(employee1.employeeName);
                editText2.setText(employee1.employeeAge);
                editText3.setText(employee1.employeeSex);
                imageView.setImageBitmap(Utils.getImage(employee1.getEmployeeimageInByte()));

            }else {
                Log.e("MA ", "No Employee available ");
            }

            return true;
        } catch (Exception e) {
            Log.e("MA ", "<loadImageFromDB> Error : " + e.getLocalizedMessage());

            return false;
        }
    }

}
---------
package com.example.pankaj.storage73;

import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.DatabaseUtils;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;

import java.util.ArrayList;

/**
 * Created by WINDOWS 8.1 on 10/20/2016.
 */

public class DBHelper extends SQLiteOpenHelper {
    public static final String DATABASE_NAME ="employeeDBName.db";
    public static final String EMPLOYEE_TABLE_NAME = "employees";
    public static final String EMPLOYEE_COLUMN_ID = "id";
    public static final String EMPLOYEEs_COLUMN_NAME = "employeeName";
    public static final String EMPLOYEEs_COLUMN_AGE = "employeeAge";
    public static final String EMPLOYEEs_COLUMN_SEX = "employeeSex";
    private static final String EMPLOYEEs_COLUMN_IMAGE = "employeeImage";
    private static final String CREATE_IMAGES_TABLE =
            "CREATE TABLE " + EMPLOYEE_TABLE_NAME + " (" +
                    EMPLOYEE_COLUMN_ID + " INTEGER PRIMARY KEY AUTOINCREMENT, "
                    + EMPLOYEEs_COLUMN_NAME + " text,"
                    + EMPLOYEEs_COLUMN_AGE + " text,"
                    + EMPLOYEEs_COLUMN_SEX + " text,"
                    + EMPLOYEEs_COLUMN_IMAGE + " BLOB NOT NULL );";

    DBHelper(Context context){
        super(context,DATABASE_NAME,null,1);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        //db.execSQL("create table employees (id integer primary key, employeeName text, employeeAge text, employeeSex text, employeeImage blob not NULL)");
        db.execSQL(CREATE_IMAGES_TABLE);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        db.execSQL("DROP TABLE IF EXISTS employees");
        onCreate(db);
    }

    public boolean insertEmployee  (String employeeName, String employeeAge, String employeeSex, byte[] employeeimageInByte) {
        SQLiteDatabase db = this.getWritableDatabase();
        ContentValues contentValues = new ContentValues();
        contentValues.put("employeeName", employeeName);
        contentValues.put("employeeAge", employeeAge);
        contentValues.put("employeeSex", employeeSex);
        contentValues.put("employeeImage", employeeimageInByte);
        db.insert("employees", null, contentValues);
        return true;
    }

    public boolean insertEmployee  (Employee employee) {
        SQLiteDatabase db = this.getWritableDatabase();
        ContentValues contentValues = new ContentValues();
        contentValues.put(EMPLOYEEs_COLUMN_NAME, employee.employeeName);
        contentValues.put(EMPLOYEEs_COLUMN_AGE, employee.employeeAge);
        contentValues.put(EMPLOYEEs_COLUMN_SEX, employee.employeeSex);
        contentValues.put(EMPLOYEEs_COLUMN_IMAGE, employee.employeeimageInByte);
        db.insert(EMPLOYEE_TABLE_NAME, null, contentValues);
        return true;
    }

    public Cursor getData(int id){
        SQLiteDatabase db = this.getReadableDatabase();
        Cursor res =  db.rawQuery( "select * from employees where id=" + id + "", null );
        return res;
    }

    public int numberOfRows(){
        int numRows;
        SQLiteDatabase db = this.getReadableDatabase();

        return numRows = (int) DatabaseUtils.queryNumEntries(db, EMPLOYEE_TABLE_NAME);
    }

    public boolean updateEmployee (Integer id, String employeeName,String employeeAge, String employeeSex)
    {
        SQLiteDatabase db = this.getWritableDatabase();
        ContentValues contentValues = new ContentValues();
        contentValues.put("employeeName", employeeName);
        contentValues.put("employeeAge", employeeAge);
        contentValues.put("employeeSex", employeeSex);
        db.update("employees", contentValues, "id = ? ", new String[] { Integer.toString(id) } );
        return true;
    }

    public Integer deleteEmployee (Integer id)
    {
        SQLiteDatabase db = this.getWritableDatabase();
        return db.delete("employees",
                "id = ? ",
                new String[] { Integer.toString(id) });
    }

    public ArrayList<Employee> getAllEmployee()
    {
        ArrayList<Employee> employeeList = new ArrayList<Employee>();
        // Select All Query
        String selectQuery = "SELECT * FROM employees";

        SQLiteDatabase db = this.getWritableDatabase();
        Cursor cursor = db.rawQuery(selectQuery, null);
        // looping through all rows and adding to list
        if (cursor.moveToFirst()) {
            do {
                Employee employee = new Employee();
                employee.setId(Integer.parseInt(cursor.getString(0)));
                employee.setEmployeeName(cursor.getString(1));
                employee.setEmployeeAge(cursor.getString(2));
                employee.setEmployeeSex(cursor.getString(3));
                employee.setEmployeeimageInByte(cursor.getBlob(4));
                // Adding contact to list
                employeeList.add(employee);
            } while (cursor.moveToNext());
        }
        // close inserting data from database
        db.close();
        // return contact list
        return employeeList;

    }
}
---------
package com.example.pankaj.storage73;

import java.sql.Blob;

/**
 * Created by WINDOWS 8.1 on 10/22/2016.
 */

public class Employee {
    int id;
    String employeeName;
    String employeeAge;
    String employeeSex;
    //Blob employeeImage;

    byte[] employeeimageInByte;

    public Employee(){

    }

    public Employee(int id,String employeeName,String employeeAge,String employeeSex,byte employeeimageInByte[]){
        this.id = id;
        this.employeeName = employeeName;
        this.employeeAge = employeeAge;
        this.employeeSex = employeeSex;
        this.employeeimageInByte = employeeimageInByte;
    }
    public Employee(String employeeName,String employeeAge,String employeeSex,byte employeeimageInByte[]){
        this.employeeName = employeeName;
        this.employeeAge = employeeAge;
        this.employeeSex = employeeSex;
        this.employeeimageInByte = employeeimageInByte;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getEmployeeName() {
        return employeeName;
    }

    public void setEmployeeName(String employeeName) {
        this.employeeName = employeeName;
    }

    public String getEmployeeAge() {
        return employeeAge;
    }

    public void setEmployeeAge(String employeeAge) {
        this.employeeAge = employeeAge;
    }

    public String getEmployeeSex() {
        return employeeSex;
    }

    public void setEmployeeSex(String employeeSex) {
        this.employeeSex = employeeSex;
    }

    public byte[] getEmployeeimageInByte() {
        return employeeimageInByte;
    }

    public void setEmployeeimageInByte(byte[] employeeimageInByte) {
        this.employeeimageInByte = employeeimageInByte;
    }


}
-------
package com.example.pankaj.storage73;

import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.InputStream;
public class Util {
    public static byte[] getImageBytes(Bitmap bitmap) {
        ByteArrayOutputStream stream = new ByteArrayOutputStream();
        bitmap.compress(Bitmap.CompressFormat.PNG, 100, stream);
        return stream.toByteArray();
    }

    public static Bitmap getImage(byte[] image) {
        return BitmapFactory.decodeByteArray(image, 0, image.length);
    }

    public static byte[] getBytes(InputStream inputStream) throws IOException {
        ByteArrayOutputStream byteBuffer = new ByteArrayOutputStream();
        int bufferSize = 1024;
        byte[] buffer = new byte[bufferSize];

        int len = 0;
        while ((len = inputStream.read(buffer)) != -1) {
            byteBuffer.write(buffer, 0, len);
        }
        return byteBuffer.toByteArray();
    }
}
