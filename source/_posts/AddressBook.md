---
title: 通讯录获取/添加信息 AddressBook
date: 2018-07-19 17:48:17
tags:
- blog
- markdown
- Android 
- 代码片段
categories:
- Android 
- 代码片段
---
源码：[`AddressBook`](https://github.com/xiaofeng0325/AddressBook)


```java
package com.example.zm.addressbook;

import android.content.ContentResolver;
import android.content.ContentUris;
import android.content.Context;
import android.database.Cursor;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.net.Uri;
import android.provider.ContactsContract;
import android.text.TextUtils;
import android.util.Log;

import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

import java.io.InputStream;
import java.util.ArrayList;
import java.util.List;

public class ContactUtil {

    /**
     * 获取联系人信息，并把数据转换成json数据
     *
     * @return
     * @throws JSONException
     */
    public static String getContactInfo(Context context) throws JSONException {
        JSONObject contactData = null;
        JSONObject jsonObject = null;
        JSONArray jsonArray = null;
        JSONObject jsonObject2 = null;
        JSONArray jsonArray2 = null;

        contactData = new JSONObject();
        jsonArray = new JSONArray();
        String mimetype = "";
        int oldrid = -1;
        int contactId = -1;
        // 1.查询通讯录所有联系人信息，通过id排序，我们看下android联系人的表就知道，所有的联系人的数据是由RAW_CONTACT_ID来索引开的
        // 所以，先获取所有的人的RAW_CONTACT_ID
        Cursor cursor = context.getContentResolver().query(ContactsContract.Data.CONTENT_URI, null, null, null, ContactsContract.Data.RAW_CONTACT_ID);
        contactData.put("userId", "11111");
        while (cursor.moveToNext()) {
            contactId = cursor.getInt(cursor.getColumnIndex(ContactsContract.Data.RAW_CONTACT_ID));
            if (oldrid != contactId) {
                jsonObject = new JSONObject();
                jsonArray2 = new JSONArray();
                jsonArray.put(jsonObject);
                oldrid = contactId;
            }
            mimetype = cursor.getString(cursor.getColumnIndex(ContactsContract.Data.MIMETYPE)); // 取得mimetype类型,扩展的数据都在这个类型里面

            Bitmap headPhoto = getHighPhoto(contactId + "", context.getContentResolver());
            if (null == headPhoto) {
                jsonObject.put("headImageUrl", "");
            } else {
                jsonObject.put("headImageUrl", headPhoto);
            }
            // 名字
            if (ContactsContract.CommonDataKinds.StructuredName.CONTENT_ITEM_TYPE.equals(mimetype)) {
                cursor.getString(cursor.getColumnIndex(ContactsContract.CommonDataKinds.StructuredName.DISPLAY_NAME));
                String firstName = cursor.getString(cursor.getColumnIndex(ContactsContract.CommonDataKinds.StructuredName.FAMILY_NAME));
                String lastname = cursor.getString(cursor.getColumnIndex(ContactsContract.CommonDataKinds.StructuredName.GIVEN_NAME));
                String trueName = firstName + lastname;
                if (TextUtils.isEmpty(trueName)) {
                    jsonObject.put("trueName", "");
                } else {
                    jsonObject.put("trueName", trueName);
                }
            }
            // 昵称
            if (ContactsContract.CommonDataKinds.Nickname.CONTENT_ITEM_TYPE.equals(mimetype)) {
                cursor.getString(cursor.getColumnIndex(ContactsContract.CommonDataKinds.Nickname.DISPLAY_NAME));
                String nickName = cursor.getString(cursor.getColumnIndex(ContactsContract.CommonDataKinds.Nickname.NAME));
                if (TextUtils.isEmpty(nickName)) {
                    jsonObject.put("nickName", "");
                } else {
                    jsonObject.put("nickName", nickName);
                }
            }
            List<AddressBookMoudle.ContactBook> contactList = new ArrayList<AddressBookMoudle.ContactBook>();
            // 1.2 获取各种电话信息
            if (ContactsContract.CommonDataKinds.Phone.CONTENT_ITEM_TYPE.equals(mimetype)) {
                int phoneType = cursor.getInt(cursor.getColumnIndex(ContactsContract.CommonDataKinds.Phone.TYPE)); // 手机
                // 个人电话
                if (phoneType == ContactsContract.CommonDataKinds.Phone.TYPE_MOBILE) {
                    String mobile = cursor.getString(cursor.getColumnIndex(ContactsContract.CommonDataKinds.Phone.NUMBER));
                    AddressBookMoudle.ContactBook contactBook = new AddressBookMoudle.ContactBook();
                    contactBook.setContact(mobile);
                    contactBook.setContactType("0");
                    contactList.add(contactBook);
                }
            }
            // Email
            if (ContactsContract.CommonDataKinds.Email.CONTENT_ITEM_TYPE.equals(mimetype)) {
                String mobileEmail = cursor.getString(cursor.getColumnIndex(ContactsContract.CommonDataKinds.Email.DATA));
                AddressBookMoudle.ContactBook contactBook = new AddressBookMoudle.ContactBook();
                contactBook.setContact(mobileEmail);
                contactBook.setContactType("1");
                contactList.add(contactBook);
            }

            if (contactList.size() > 0) {
                for (int i = 0; i < contactList.size(); i++) {
                    jsonObject2 = new JSONObject();
                    jsonObject2.put("contact", contactList.get(i).getContact());
                    jsonObject2.put("contactType", contactList.get(i).getContactType());
                    jsonArray2.put(jsonObject2);
                }
                jsonObject.put("contactList", jsonArray2);

            }

            // 查找event地址
            if (ContactsContract.CommonDataKinds.Event.CONTENT_ITEM_TYPE.equals(mimetype)) { // 取出时间类型
                int eventType = cursor.getInt(cursor.getColumnIndex(ContactsContract.CommonDataKinds.Event.TYPE));
                // 生日
                if (eventType == ContactsContract.CommonDataKinds.Event.TYPE_BIRTHDAY) {
                    String birthday = cursor.getString(cursor.getColumnIndex(ContactsContract.CommonDataKinds.Event.START_DATE));
                    if (TextUtils.isEmpty(birthday)) {
                        jsonObject.put("birthDay", "");
                    } else {
                        jsonObject.put("birthDay", birthday);
                    }
                }
            }

            // 获取组织信息
            if (ContactsContract.CommonDataKinds.Organization.CONTENT_ITEM_TYPE.equals(mimetype)) { // 取出组织类型
                int orgType = cursor.getInt(cursor.getColumnIndex(ContactsContract.CommonDataKinds.Organization.TYPE)); // 单位
                if (orgType == ContactsContract.CommonDataKinds.Organization.TYPE_CUSTOM) { // if (orgType ==
                    // Organization.TYPE_WORK)
                    // {
                    String company = cursor.getString(cursor.getColumnIndex(ContactsContract.CommonDataKinds.Organization.COMPANY));
                    if (TextUtils.isEmpty(company)) {
                        jsonObject.put("company", "");
                    } else {
                        jsonObject.put("company", company);
                    }
                    String jobTitle = cursor.getString(cursor.getColumnIndex(ContactsContract.CommonDataKinds.Organization.TITLE));
                    if (TextUtils.isEmpty(jobTitle)) {
                        jsonObject.put("jobtitle", "");
                    } else {
                        jsonObject.put("jobtitle", jobTitle);
                    }
                    String department = cursor.getString(cursor.getColumnIndex(ContactsContract.CommonDataKinds.Organization.DEPARTMENT));
                    if (TextUtils.isEmpty(department)) {
                        jsonObject.put("department", "");
                    } else {
                        jsonObject.put("department", department);
                    }
                }
            }
        }

        cursor.close();
        contactData.put("addressBookList", jsonArray);
        Log.i("contactData", contactData.toString());
        return contactData.toString();
    }


    /**
     * 获取联系人高清头像
     *
     * @param people_id 联系人ID
     * @param cr        调用容器
     * @return 联系人的高清头像
     */
    public static Bitmap getHighPhoto(String people_id, ContentResolver cr) {
        Uri uri = ContentUris.withAppendedId(ContactsContract.Contacts.CONTENT_URI, Long.parseLong(people_id));
        InputStream input = ContactsContract.Contacts.openContactPhotoInputStream(cr, uri, true);
        if (input == null) {
            return null;
        }
        return BitmapFactory.decodeStream(input);
    }

}

```

```json
{
	"userId": "11111",
	"addressBookList": [{
		"headImageUrl": "",
		"trueName": "张三",
		"company": "北京xxx有限公司",
		"jobtitle": "研发工程师",
		"department": "",
		"contactList": [{
			"contact": "185 0000 1234",
			"contactType": "0"
		}, {
			"contact": "zhangsan@163.com",
			"contactType": "1"
		}],
		"birthDay": "2018-03-25"
	}]
}
```
