---
title: Odesílání nabízených oznámení konkrétním aplikacím pro Android pomocí Azure Notification Hubs
description: Zjistěte, jak pomocí služby Azure Notification Hubs posílat nabízená oznámení konkrétním uživatelům.
documentationcenter: android
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ae0e17a8-9d2b-496e-afd2-baa151370c25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/11/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/11/2019
ms.openlocfilehash: c2d3789082130cbbc42021a0706249dd3966b9ef
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75531118"
---
# <a name="tutorial-send-push-notifications-to-specific-android-apps-using-azure-notification-hubs"></a>Kurz: Odesílání nabízených oznámení do konkrétních aplikací pro Android pomocí center oznámení Azure

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

V tomto kurzu se dozvíte, jak pomocí služby Azure Notification Hubs posílat nabízená oznámení konkrétním uživatelům aplikace na konkrétním zařízení. K ověřování klientů a generování oznámení se používá back-end ASP.NET WebAPI, jak je znázorněno v článku s doprovodnými materiály popisujícím [registraci z back-endu aplikace](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend). Tento kurz vychází z centra oznámení, které jste vytvořili v [kurzu: Nabízená oznámení na zařízení se systémem Android pomocí center oznámení Azure a Cloud Messaging Firebase](notification-hubs-android-push-notification-google-fcm-get-started.md).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření projektu back-endového webového rozhraní API pro ověřování uživatelů  
> * Aktualizace aplikace pro Android
> * Otestování aplikace

## <a name="prerequisites"></a>Požadavky

Před provedením tohoto kurzu [dokončete kurz: Nabízená oznámení do zařízení androida pomocí center oznámení Azure a cloudového zasílání zpráv Firebase.](notification-hubs-android-push-notification-google-fcm-get-started.md)

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="create-the-android-project"></a>Vytvoření projektu Android

Dalším krokem je aktualizace aplikace pro Android vytvořené v [kurzu: Nabízená oznámení na zařízení se systémem Android pomocí Center oznámení Azure a Firebase Cloud Messaging](notification-hubs-android-push-notification-google-fcm-get-started.md).

1. Otevřete `res/layout/activity_main.xml` soubor a nahraďte následující definice obsahu:

    Tím se přidají ovládací prvky textových polí umožňující přihlášení uživatele. Přidá se také pole pro značku uživatelského jména, která bude součástí oznámení, která odešlete:

    ```xml
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/usernameText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="@string/usernameHint"
        android:layout_above="@+id/passwordText"
        android:layout_alignParentEnd="true" />
    <EditText
        android:id="@+id/passwordText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="@string/passwordHint"
        android:inputType="textPassword"
        android:layout_above="@+id/buttonLogin"
        android:layout_alignParentEnd="true" />
    <Button
        android:id="@+id/buttonLogin"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/loginButton"
        android:onClick="login"
        android:layout_above="@+id/toggleButtonFCM"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="24dp" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="WNS on"
        android:textOff="WNS off"
        android:id="@+id/toggleButtonWNS"
        android:layout_toLeftOf="@id/toggleButtonFCM"
        android:layout_centerVertical="true" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="FCM on"
        android:textOff="FCM off"
        android:id="@+id/toggleButtonFCM"
        android:checked="true"
        android:layout_centerHorizontal="true"
        android:layout_centerVertical="true" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="APNS on"
        android:textOff="APNS off"
        android:id="@+id/toggleButtonAPNS"
        android:layout_toRightOf="@id/toggleButtonFCM"
        android:layout_centerVertical="true" />
    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessageTag"
        android:layout_below="@id/toggleButtonFCM"
        android:layout_centerHorizontal="true"
        android:hint="@string/notification_message_tag_hint" />
    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_below="@+id/editTextNotificationMessageTag"
        android:layout_centerHorizontal="true"
        android:hint="@string/notification_message_hint" />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:onClick="sendNotificationButtonOnClick"
        android:layout_below="@+id/editTextNotificationMessage"
        android:layout_centerHorizontal="true" />
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        android:id="@+id/text_hello"
        />
    </RelativeLayout>
    ```
2. Otevřete `res/values/strings.xml` soubor a `send_button` nahraďte definici následujícími řádky, které předefinují řetězec pro `send_button` a přidávají řetězce pro ostatní ovládací prvky:

    ```xml
    <string name="usernameHint">Username</string>
    <string name="passwordHint">Password</string>
    <string name="loginButton">1. Sign in</string>
    <string name="send_button">2. Send Notification</string>
    <string name="notification_message_hint">Notification message</string>
    <string name="notification_message_tag_hint">Recipient username</string>
    ```

    Grafické `main_activity.xml` rozložení by nyní mělo vypadat jako na následujícím obrázku:

    ![][A1]
3. Vytvořte novou `RegisterClient` třídu s názvem `MainActivity` ve stejném balíčku jako vaše třída. Pro soubor s novou třídou použijte následující kód.

    ```java
  
    import java.io.IOException;
    import java.io.UnsupportedEncodingException;
    import java.util.Set;
    
    import org.apache.http.HttpResponse;
    import org.apache.http.HttpStatus;
    import org.apache.http.client.ClientProtocolException;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.client.methods.HttpPut;
    import org.apache.http.client.methods.HttpUriRequest;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.DefaultHttpClient;
    import org.apache.http.util.EntityUtils;
    import org.json.JSONArray;
    import org.json.JSONException;
    import org.json.JSONObject;
    
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.util.Log;
    
    public class RegisterClient {
        private static final String PREFS_NAME = "ANHSettings";
        private static final String REGID_SETTING_NAME = "ANHRegistrationId";
        private String Backend_Endpoint;
        SharedPreferences settings;
        protected HttpClient httpClient;
        private String authorizationHeader;
    
        public RegisterClient(Context context, String backendEndpoint) {
            super();
            this.settings = context.getSharedPreferences(PREFS_NAME, 0);
            httpClient =  new DefaultHttpClient();
            Backend_Endpoint = backendEndpoint + "/api/register";
        }
    
        public String getAuthorizationHeader() {
            return authorizationHeader;
        }
    
        public void setAuthorizationHeader(String authorizationHeader) {
            this.authorizationHeader = authorizationHeader;
        }
    
        public void register(String handle, Set<String> tags) throws ClientProtocolException, IOException, JSONException {
            String registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
    
            JSONObject deviceInfo = new JSONObject();
            deviceInfo.put("Platform", "fcm");
            deviceInfo.put("Handle", handle);
            deviceInfo.put("Tags", new JSONArray(tags));
    
            int statusCode = upsertRegistration(registrationId, deviceInfo);
    
            if (statusCode == HttpStatus.SC_OK) {
                return;
            } else if (statusCode == HttpStatus.SC_GONE){
                settings.edit().remove(REGID_SETTING_NAME).commit();
                registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
                statusCode = upsertRegistration(registrationId, deviceInfo);
                if (statusCode != HttpStatus.SC_OK) {
                    Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                    throw new RuntimeException("Error upserting registration");
                }
            } else {
                Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                throw new RuntimeException("Error upserting registration");
            }
        }
    
        private int upsertRegistration(String registrationId, JSONObject deviceInfo)
                throws UnsupportedEncodingException, IOException,
                ClientProtocolException {
            HttpPut request = new HttpPut(Backend_Endpoint+"/"+registrationId);
            request.setEntity(new StringEntity(deviceInfo.toString()));
            request.addHeader("Authorization", "Basic "+authorizationHeader);
            request.addHeader("Content-Type", "application/json");
            HttpResponse response = httpClient.execute(request);
            int statusCode = response.getStatusLine().getStatusCode();
            return statusCode;
        }
    
        private String retrieveRegistrationIdOrRequestNewOne(String handle) throws ClientProtocolException, IOException {
            if (settings.contains(REGID_SETTING_NAME))
                return settings.getString(REGID_SETTING_NAME, null);
    
            HttpUriRequest request = new HttpPost(Backend_Endpoint+"?handle="+handle);
            request.addHeader("Authorization", "Basic "+authorizationHeader);
            HttpResponse response = httpClient.execute(request);
            if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                Log.e("RegisterClient", "Error creating registrationId: " + response.getStatusLine().getStatusCode());
                throw new RuntimeException("Error creating Notification Hubs registrationId");
            }
            String registrationId = EntityUtils.toString(response.getEntity());
            registrationId = registrationId.substring(1, registrationId.length()-1);
    
            settings.edit().putString(REGID_SETTING_NAME, registrationId).commit();
    
            return registrationId;
        }
    }
    ```

    Tato součást implementuje volání REST potřebné ke kontaktování back-endu aplikace k registraci nabízených oznámení. Kromě toho místně ukládá *ID registrací* vytvořená centrem oznámení, jak je podrobně popsáno v tématu popisujícím [registraci z back-endu aplikace](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend). Po klepnutí na tlačítko **Přihlásit** se použije autorizační token uložený v místním úložišti.
4. Ve `MainActivity` své třídě a přidejte `RegisterClient` pole pro třídu a řetězec pro koncový bod back-endu ASP.NET. Nezapomeňte nahradit `<Enter Your Backend Endpoint>` skutečným koncovým bodem vašeho back-endu, který jste předtím získali. Například, `http://mybackend.azurewebsites.net`.

    ```java
    private RegisterClient registerClient;
    private static final String BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    FirebaseInstanceId fcm;
    String FCM_token = null;
    ```

5. V třídě `MainActivity` v metodě `onCreate` odeberte nebo okomentujte inicializaci pole `hub` a volání metody `registerWithNotificationHubs`. Pak přidejte kód pro inicializaci instance třídy `RegisterClient`. Metoda by měla obsahovat následující řádky:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mainActivity = this;
        FirebaseService.createChannelAndHandleNotifications(getApplicationContext());
        fcm = FirebaseInstanceId.getInstance();
        registerClient = new RegisterClient(this, BACKEND_ENDPOINT);
        setContentView(R.layout.activity_main);
    }
    ```
7. Přidejte do souboru `MainActivity.java` následující příkazy `import`.

    ```java
    import android.util.Base64;
    import android.view.View;
    import android.widget.EditText;
    
    import android.widget.Button;
    import android.widget.ToggleButton;
    import java.io.UnsupportedEncodingException;
    import android.content.Context;
    import java.util.HashSet;
    import android.widget.Toast;
    import org.apache.http.client.ClientProtocolException;
    import java.io.IOException;
    import org.apache.http.HttpStatus;
    
    import android.os.AsyncTask;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.DefaultHttpClient;
    
    import android.app.AlertDialog;
    import android.content.DialogInterface;
    
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.google.android.gms.tasks.OnSuccessListener;
    import java.util.concurrent.TimeUnit;
    ```
8. Nahraďte kód metody onStart následujícím kódem:

    ```java
    super.onStart();
    Button sendPush = (Button) findViewById(R.id.sendbutton);
    sendPush.setEnabled(false);
    ```
9. Potom přidejte následující metody pro zpracování události kliknutí na tlačítko **Přihlásit** a odesílání nabízených oznámení.

    ```java
    public void login(View view) throws UnsupportedEncodingException {
        this.registerClient.setAuthorizationHeader(getAuthorizationHeader());

        final Context context = this;
        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {

                    FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() {
                        @Override
                        public void onSuccess(InstanceIdResult instanceIdResult) {
                            FCM_token = instanceIdResult.getToken();
                            Log.d(TAG, "FCM Registration Token: " + FCM_token);
                        }
                    });
                    TimeUnit.SECONDS.sleep(1);
                    registerClient.register(FCM_token, new HashSet<String>());
                } catch (Exception e) {
                    DialogNotify("MainActivity - Failed to register", e.getMessage());
                    return e;
                }
                return null;
            }

            protected void onPostExecute(Object result) {
                Button sendPush = (Button) findViewById(R.id.sendbutton);
                sendPush.setEnabled(true);
                Toast.makeText(context, "Signed in and registered.",
                        Toast.LENGTH_LONG).show();
            }
        }.execute(null, null, null);
    }

    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
        return basicAuthHeader;
    }

    /**
        * This method calls the ASP.NET WebAPI backend to send the notification message
        * to the platform notification service based on the pns parameter.
        *
        * @param pns     The platform notification service to send the notification message to. Must
        *                be one of the following ("wns", "fcm", "apns").
        * @param userTag The tag for the user who will receive the notification message. This string
        *                must not contain spaces or special characters.
        * @param message The notification message string. This string must include the double quotes
        *                to be used as JSON content.
        */
    public void sendPush(final String pns, final String userTag, final String message)
            throws ClientProtocolException, IOException {
        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {

                    String uri = BACKEND_ENDPOINT + "/api/notifications";
                    uri += "?pns=" + pns;
                    uri += "&to_tag=" + userTag;

                    HttpPost request = new HttpPost(uri);
                    request.addHeader("Authorization", "Basic "+ getAuthorizationHeader());
                    request.setEntity(new StringEntity(message));
                    request.addHeader("Content-Type", "application/json");

                    HttpResponse response = new DefaultHttpClient().execute(request);

                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        DialogNotify("MainActivity - Error sending " + pns + " notification",
                                response.getStatusLine().toString());
                        throw new RuntimeException("Error sending notification");
                    }
                } catch (Exception e) {
                    DialogNotify("MainActivity - Failed to send " + pns + " notification ", e.getMessage());
                    return e;
                }

                return null;
            }
        }.execute(null, null, null);
    }
    ```

    Obslužná rutina `login` pro tlačítko **Přihlásit** generuje základní ověřovací token pomocí vstupní uživatelské jméno a `RegisterClient` heslo (představuje libovolný token, který používá vaše schéma ověřování), pak se používá k volání back-endu pro registraci.

    Metoda `sendPush` zavolá back-end za účelem aktivace zabezpečeného oznámení pro uživatele na základě značky uživatele. Systém oznámení platformy, na který metoda `sendPush` cílí, závisí na předaném řetězci `pns`.

10. Do třídy `MainActivity` přidejte následující metodu `DialogNotify`.

    ```java
    protected void DialogNotify(String title, String message)
    {
        AlertDialog alertDialog = new AlertDialog.Builder(MainActivity.this).create();
        alertDialog.setTitle(title);
        alertDialog.setMessage(message);
        alertDialog.setButton(AlertDialog.BUTTON_NEUTRAL, "OK",
                new DialogInterface.OnClickListener() {
                    public void onClick(DialogInterface dialog, int which) {
                        dialog.dismiss();
                    }
                });
        alertDialog.show();
    }
    ```
11. V třídě `MainActivity` následujícím způsobem aktualizujte metodu `sendNotificationButtonOnClick`, aby volala metodu `sendPush` s použitím systému oznámení platformy vybraného uživatelem.

    ```java
    /**
    * Send Notification button click handler. This method sends the push notification
    * message to each platform selected.
    *
    * @param v The view
    */
    public void sendNotificationButtonOnClick(View v)
            throws ClientProtocolException, IOException {

        String nhMessageTag = ((EditText) findViewById(R.id.editTextNotificationMessageTag))
                .getText().toString();
        String nhMessage = ((EditText) findViewById(R.id.editTextNotificationMessage))
                .getText().toString();

        // JSON String
        nhMessage = "\"" + nhMessage + "\"";

        if (((ToggleButton)findViewById(R.id.toggleButtonWNS)).isChecked())
        {
            sendPush("wns", nhMessageTag, nhMessage);
        }
        if (((ToggleButton)findViewById(R.id.toggleButtonFCM)).isChecked())
        {
            sendPush("fcm", nhMessageTag, nhMessage);
        }
        if (((ToggleButton)findViewById(R.id.toggleButtonAPNS)).isChecked())
        {
            sendPush("apns", nhMessageTag, nhMessage);
        }
    }
    ```
12. V `build.gradle` souboru přidejte následující `android` řádek do `buildTypes` oddílu za oddíl.

    ```java
    useLibrary 'org.apache.http.legacy'
    ```
13. Pokud vaše aplikace cílí na úroveň rozhraní API 28 (Android 9.0) nebo vyšší, uveďte do `<application>` prvku . `AndroidManifest.xml`

    ```xml
    <uses-library
        android:name="org.apache.http.legacy"
        android:required="false" />
    ```
14. Sestavte projekt.

## <a name="test-the-app"></a>Otestování aplikace

1. Pomocí Android Studia spusťte aplikaci na zařízení nebo v emulátoru.
2. V aplikaci pro Android zadejte uživatelské jméno a heslo. Oba řetězce musí mít stejnou hodnotu a nesmí obsahovat mezery ani speciální znaky.
3. V aplikaci pro Android klikněte na **Přihlásit se**. Počkejte na informační zprávu s **upisem Přihlášeno a zaregistrováno**. Tím se aktivuje tlačítko **Send Notification** (Odeslat oznámení).

    ![][A2]
4. Kliknutím na přepínací tlačítko povolte všechny platformy, na kterých jste aplikaci spustili a zaregistrovali uživatele.
5. Zadejte jméno uživatele, který obdrží zprávu oznámení. Tento uživatel musí mít na cílových zařízení zaregistrovaná oznámení.
6. Zadejte zprávu, kterou uživatel obdrží jako nabízené oznámení.
7. Klikněte na **Send Notification** (Odeslat oznámení).  Nabízené oznámení obdrží všechna zařízení, jejichž registrace odpovídá značce uživatelského jména.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak posílat nabízená oznámení konkrétním uživatelům, k jejichž registracím jsou přidružené značky. V dalším kurzu se dozvíte, jak posílat nabízená oznámení na základě polohy:

> [!div class="nextstepaction"]
>[Odesílání oznámení na základě polohy](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[A1]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users.png
[A2]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users-enter-password.png
