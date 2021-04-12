title: Android使用XmlPullParser解析xml文件
tags:
  - android
  - xml
id: 13
categories:
  - Android
date: 2015-03-12 15:23:56
premalink: android_edittext_no_key
thumbnailImage: http://qn-jiezhi.nospider.net/android1119002570721cd2c8o.jpg-thumb1
metaAlignment: center
coverImage: http://qn-jiezhi.nospider.net/10052206,2560,1600.jpg
coverCaption: "Android"
coverMeta: out
photos:
comments: true

---

```java
private void parse() {
  try {
    XmlPullParserFactory factory = XmlPullParserFactory.newInstance();
    factory.setNamespaceAware(false);
    XmlPullParser xpp = factory.newPullParser();

    xpp.setInput(new FileReader(filePath));
    int eventType = xpp.getEventType();
    while (eventType != XmlPullParser.END_DOCUMENT) {
      if (eventType == XmlPullParser.START_DOCUMENT) {
        System.out.println(&quot;Start document&quot;);
      } else if (eventType == XmlPullParser.START_TAG) {
        System.out.println(&quot;Start tag &quot; + xpp.getName());
      } else if (eventType == XmlPullParser.END_TAG) {
        System.out.println(&quot;end tag &quot; + xpp.getName());
      } else if (eventType == XmlPullParser.TEXT) {
        System.out.println(&quot;Text &quot; + xpp.getText());
      }

      eventType = xpp.next();
    }
    System.out.println(&quot;End docment&quot;);
  } catch (XmlPullParserException e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
  } catch (FileNotFoundException e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
  } catch (IOException e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
  }
}
```
