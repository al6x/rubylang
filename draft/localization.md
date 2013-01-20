# Localization

You probably know about the I18n - it does translation and does it well. But it solves only part of multilingual support, and there are a lots of docs about how-to use it, let's talk about another not so easy part.

Sometimes You need **not only translated labels and dates - but also to share content in multiple languages**. It creates a new problem - how and where do You store multiple versions of content?
As far as I know there are two main approaches **store multiple versions of content in the model and have one site** and **store only one version of content in the model but have multiple sites**.

Let's take a first one - multiple versions of content in the model:

- usually You have a lots of models, and it having a lots of attributes and a lots of those attributes needs to be translated
- sometimes You also need to have different images (model attachment) for different languages
- You also need to design UI to allow easy and handy switching between translations for both users and editors
- all of this stuff has to be tested and always kept in mind during re-factoring, migrating, updating an so on.

For concrete sample - let's take this site: this post (and not only the :thext but also all other of it's attributes and corresponding objects: title, tags, slug, tags cloud, navigation, ...)  needs to be available in both languages - english and [russian][russian_article], logo - it's also different in english and russian version.

I tried the first approach at first, but quickly rejected it, it adds too many complexity to the project. So I tried the second approach - multiple sites, one for each language, and it seems it works (actually it's the same site, but it has kind of "virtual workspaces" for each language).

As for UI controls - it's also simple - the only I need is a simple navigation at the top right corner.

This approach also has it's limitations - for example all those sites are independent and for example  - You has to setup navigation for each separately.

Tags : I18n, Internalization, Localization, Multilingual
Date : 2010/6/1

[russian_article]:  /blog-ru/lokalizatsiia-640