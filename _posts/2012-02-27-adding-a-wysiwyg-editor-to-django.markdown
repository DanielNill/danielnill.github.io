---
title: Adding a WYSIWYG Editor to Django Admin
layout: base
alias: /blog/adding-a-wysiwyg-editor-to-django-admin/
---

This will tell you pretty much everything you need to know about adding a WYSIWYG editor or any other javascript based features you may want to your django admin.

For most modern WYSIWYG editors installation is just a matter of adding a few lines of javascript and possibly adding a class or id to the textareas you want effected.  [This django wiki entry](https://code.djangoproject.com/wiki/AddWYSIWYGEditor) gets you about half the way depending on the editor you want to use, but there are a few other tricks you can use depending on your editor of choice.  Here I'll show the instalation process for  Tiny MCE and CKEditor, two of the more popular WYSIWYGs, both of which use different installation methods, to show you the modifications you can make to get most editors working.

###Tiny MCE

Tiny MCE isn't a great editor and if given the choice I would suggest switching to something else (like CKEditor outlined below). But that's niether here nor there.  It's very popular, and provides a good example of methods you can use to install javascript plugins in the django admin.

So for Tiny MCE first you want to create a javascript file that has the basic installaiton script, we will call it textarea.js:

{% highlight javascript %}
tinyMCE.init({
  mode : "textareas",
  theme : "advanced",
  theme_advanced_toolbar_location : "top",
  theme_advanced_toolbar_align : "left",
  plugins : "table, save, advhr, advimage, advlink, emotions, iespell, insertdatetime, preview",
  theme_advanced_buttons1 : "fullscreen, separator, preview, separator, bold, italic, underline, strikethrough, separator, bullist, numlist, outdent, indent, separator, undo, redo, separator, link, unlink, anchor, separator, image, cleanup, help, separator, code",
  auto_cleanup_word : true,
  plugin_insertdate_dateFormat : "%m/%d/%Y",
  plugin_insertdate_timeFormat : "%H:%M:%S",
  extended_valid_elements : "a[name|href|target=_blank|title|onclick], img[class|src|border=0|alt|title|hspace|vspace|width|height|align|onmouseover|onmouseout|name], hr[class|width|size|noshade], font[face|size|color|style], span[class|align|style]"
});
{% endhighlight %}
You can find the details of setting up your layout here, but the basic thing you need is a javascript file with

{% highlight javascript %}
tinyMCE.init({mode: "textarea", ...});
{% endhighlight %}
Now, the key to adapting this method to installing other WYSIWYG editors is knowing that you can include any javascript file in the django admin.  To do this you simply need to add a media subclass to your custom admin model.

{% highlight python %}
class SomeModelAdmin(admin.ModelAdmin):
    class Media:
        js = (
            '/media/tiny_mce/tiny_mce.js',
            '/media/textareas.js',
        )
{% endhighlight %}
This will load the resources listed into the /admin/app_name/model_name/.  As you can see for Tiny MCE you need to include the textareas.js file we just made and the main Tiny MCE script which I choose to store in my media directory.

For Tiny MCE that is all you need.  The WYSIWYG will be included on all textareas in your admin.

###CKEditor

CKEditor is a much nicer editor.  It's primary drawback, however, is its limit documentation.  Luckily, because it is a nice editor there are plenty of tutorials for installation online.  The primary difference between installation for CKEditor and Tiny MCE is that you cannot assign the plugin to generic textareas like you can Tiny MCE. You must assign the editor by class or id.

But this is not a problem.  We just modify the django admin to have a custom class assigned to textareas.

{% highlight python %}
from django import forms

class SomeModelAdmin(admin.ModelAdmin):
    formfield_overrides = { models.TextField: {'widget': forms.Textarea(attrs={'class':'ckeditor'})}, }

    class Media:
        js = ('/media/ckeditor/ckeditor.js',)
{% endhighlight %}
Note that we are still including the ckeditor javascript file.  But a custom javascript file is not needed unless you want to modify the WYSIWYG's layout.

So that is pretty much all you need to know.  Between those two methods you should be able to add most javascript based plugins to your django admin.
