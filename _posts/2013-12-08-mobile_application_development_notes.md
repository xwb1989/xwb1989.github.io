---
layout: post
title: Mobile Application Development Notes
category: blog
---

#Mobile Application Development Notes

##BroadcastReceiver
You can either register/unregister broadcast receiver dynamically in onResume() and onPause() or do it statically in AndroidManifest.xml.

##Threading
Use a new thread to do background work. If need to modify the UI thread, you cannot modify it through the new thread(that's unsafe). The modification on UI should be done via the view's post() method, which invokes a new thread to queue and add the modification on the view.

##Fragment
You can not inflate a layout when there is already fragment existing. Normally that's OK because that fragment will be destroy in the onViewDestroy() default when you leave the fragment. But if you inflate the layout with nested fragment, then the nested fragment might able to be removed in default. In this case, you might need to remove the nested fragment manually by referring its id. Otherwise, you might need to only inflate the layout once. 