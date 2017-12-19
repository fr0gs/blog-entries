# Problem

I am currently working on an issue in a very old Ember.js application (version 1.13.0 of the ember-cli). After performing the regular ritual: `npm install; bower install; ember s --proxy <proxy>` I was presented with the application's login screen, but what was my surprise that after introducing a valid set of credentials, the next http call invariably failed.

```
Client -> POST /sessions -> Server API (works)
Client -> GET /settings -> Server API (fails)
```

Now, the code wasn't changed in the frontend so there was a slight chance the backend would be the culprit. Checking the differences between a working production environment two things stood out:

* First environment both frontend & backend are in the same server. While developing with `ember s` I have a server running locally and I make calls to the external API, so this might be a problem of related with making cross-origin requests. 
* After being successfully authenticated, the server sends a session cookie using the **Set-Cookie** header, and in subsequent calls the browser will send that cookie in the request headers. Usual behavior. But in the failing environment, after a successful login, the browser received the cookie but never sent it back again.

I quickly discarded the first case, since when you use `ember s --proxy` you are really making a call to the same origin and then the proxy will route it to the external API. On the other side... why was my cookie not being set?! It was not until a coworker pointed out the *Secure* flag in the session cookie. What was that?

It turns out, as seen [here](https://www.owasp.org/index.php/SecureFlag), the *Secure* flag is set to prevent third parties to observe sensitive cookies being sent in clear text, hence, those cookies won't be resent unless they are received & sent over HTTPS, and the development server was running over regular HTTP.


# Solution

The solution was indeed quite simple! The **ember-cli** provides an easy way to serve traffic over SSL using a personal certificate. For developing purposes, I just created a self-signed certificate following the instructions in the [heroku dev center](https://devcenter.heroku.com/articles/ssl-certificate-self). After that you would have a **server.key** and **server.crt** key & certificate.

Then you can run the ember-cli specifying the ssl options in **.ember-cli**:

```sh
{
  "disableAnalytics": false,
  "ssl": true,
  "ssl-key": "path/to/server.key",
  "ssl-cert": "path/to/server.crt"
}
```

By default ember will look into the `ssl/` folder for the **server.key** and the **server.crt** files, so creating those files inside the folder and running

```
$ ember s --ssl=true --proxy <proxy>
```

Will make the thing run.



Have fun!

