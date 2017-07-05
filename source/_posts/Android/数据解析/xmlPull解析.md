---
title: XML Pull解析代码
date: 2016-03-25 21:18:12
tags: xml,pull
category: android

---

# XML Pull解析代码
解析地址：[http://www.inexus.co/portal.php?mod=rss](http://www.inexus.co/portal.php?mod=rss)

<!--more-->
解析实体类：

	public class RssItem {

	    private String title;
	    private String link;
	    private String category;
	    private String author;
	。。。
	}

解析代码：

	XmlPullParserFactory factory = null;
	try {
		factory = XmlPullParserFactory.newInstance();
		XmlPullParser parser = factory.newPullParser();
		parser.setInput(body.charStream());
		ArrayList<RssItem> list = new ArrayList<>();
		while (parser.next() != XmlPullParser.END_DOCUMENT) {
			switch (parser.getEventType()) {
				case XmlPullParser.START_TAG:
					switch (parser.getName()) {
						case "item":
							list.add(new RssItem());
							break;
						case "title":
							if (!list.isEmpty()) {
								list.get(list.size() - 1).setTitle(parser.nextText());
							}
							break;
						case "link":
							if (!list.isEmpty()) {
								list.get(list.size() - 1).setLink(parser.nextText());
							}
							break;
						case "category":
							if (!list.isEmpty()) {
								list.get(list.size() - 1).setCategory(parser.nextText());
							}
							break;
						case "author":
							if (!list.isEmpty()) {
								list.get(list.size() - 1).setAuthor(parser.nextText());
							}
							break;
					}
					break;
			}

		}
		Log.d(TAG, "onResponse: " + list.toString());

		} catch (XmlPullParserException e) {
			e.printStackTrace();
	}