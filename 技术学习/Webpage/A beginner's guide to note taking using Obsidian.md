## Learn to build your second brain using Obsidian

Published on January 16, 2021

I've always taken notes.

Sometimes they are paragraphs in a notebook.

Sometimes they are short sentences on post-its.

I've created collections of documents in Google Drive. I've got hundreds of notebooks in Evernote. I have a Dynalist outliner which is overflowing with notes.

One thing is a constant though.

**I rarely look at them again! 😱**

This year I discovered the idea of [personal knowledge management](https://en.wikipedia.org/wiki/Personal_knowledge_management) (PKM). This is the idea of collecting and classifying your thoughts so that they can be easily retrieved but also help you to make sense out of them. It hadn't occured to me that I should be bringing all of my notes together so that I could build new ideas from them. I mostly used the search functionality in any notes applications to find things I'd recorded when I needed them. Reading about PKM brought me to a tool called [Roam Research](https://roamresearch.com/) which shared the idea of networked thought. It gives the user a way to link notes together in a more coherent way to create a graph of connected thinking. I used this for a while but I was looking for something in the open source space. And then I finally discovered [Obsidian](https://obsidian.md/).

Using Obsidian for the last 4 or 5 months has really helped me to organise my notes and has started me on a journey to creating new ideas from concepts I find rather than letting the notes wither away and die.

Obsidian is a tool which gives you "a [second brain](https://fortelabs.co/blog/basboverview/), forever". It's a linked knowledge base similar to Roam Research, but it allows you to store your own [Markdown](https://www.markdownguide.org/) files on your computer rather than in the cloud. As a developer, Markdown is a format I write in regularly and this made it a perfect tool for me. As the files are stored locally, you never have to worry about a service being shut down or changing their pricing model. Of course, you're now responsible for backing up your data but this is quickly achievable using Google Drive, Dropbox, OneDrive, or some other online storage method.

One of the great ideas behind Obsidian and Roam Research is the idea of linked notes. By tagging concepts in your notes, you slowly build up a graph which helps to draw you towards similar ideas and thinking. Here's an example of my current set of notes in Obsidian:

![Obsidian Graph](https://www.marclittlemore.com/images/posts/obsidian-graph.jpg)

At the time of writing this, Obsidian is available for Mac, Windows, and Linux. Download the version for your operating system [on their website](https://obsidian.md/download). Once installed you'll be asked to create or open a vault. This vault is simply a directory within which your notes will be stored as Markdown files. Choose "create" if you don't have any files that you've already created. Alternatively you can open a folder which contains existing Markdown files and they will be imported into Obsidian.

Once you're set up you can click on "create note" to create a new Markdown file for a new note.

Each new note that's created is a Markdown file. [Markdown syntax](https://www.markdownguide.org/getting-started/) is relatively easy to get started with. It's a plaintext file format with specific syntax which maps to HTML elements. Here's an example

```
# A main headerSome text as a paragraph. But this text is **bold** and this is in *italics*.## A secondary headerAnother paragraph with some `inline code` in it.A [link](/another-page) to another page.
```

While Markdown supports hyperlinking to another internal or external page, Obsidian adds the idea of the backlink. Most links are mono-directional and just point to that new page. Backlinks allow bi-directional linking. This means that if page A links to page B, Obsidian also knows that page B is part of page A. This allows ideas to start linking together as you write new notes.

Obsidian backlinks are created using the [Wikilink](https://en.wikipedia.org/wiki/Help:Link#Wikilinks_(internal_links)) format using double brackets. Here is an example of how to create a note which links to another page:

```
# My new noteI want this note to link to [[another note]] using Obisidian backlinks.
```

By default, Obsidian will link to a new note but it won't create it until you follow the link. Pressing `CMD + click` (Mac) or `CTRL + click` (Windows) on the link will take you to a new page and then the note is created in your vault.

### Linked and unlinked mentions

Obsidian backlinks are really useful for seeing which ideas link together in your notes. It has the concept of linked and unlinked mentions. Linked mentions are when you explicitly link to a page using the `[[backlink]]` format. Unlinked mentions are when you mention the page in another note but you don't directly link to it. Using these mentions, you can quickly see how ideas are related. This can expand your thoughts and allow you to create new ideas from them.

Here's an example of some linked and unlinked mentions from when I was building a [Google Firebase](https://firebase.google.com/) project and documenting my learning:

![Obsidian linked and unlinked mentions](https://www.marclittlemore.com/images/posts/obsidian-linked-unlinked-mentions.jpg)

### Tags

In addition to linking notes together, Obsidian allows you to use tags. Adding tags to a note can make them easier to find in searches if you have a large vault of notes. They're helpful for adding context to your notes and allowing you to group them together. In your Markdown files you add a new tag using a Twitter-style hashtag. For example, I use `#toread` or `#towatch` to gather blog posts or YouTube videos I should watch and take notes from.

Obsidian allows you to create an unlimited vault of notes but its power comes in linking these together. It allows you to visualise connections between your notes and encourages you to explore how your thinking is linked together. Bring up this view by clicking on "open graph view" in the sidebar. The graph view allows you to filter by specific keywords or tags that you use in your notes. Here's an example of notes related to my use of Obsidian:

![Obsidian graph view](https://www.marclittlemore.com/images/posts/obsidian-graph-view.jpg)

I've only scratched the surface with what Obsidian can do for your personal knowledge management system. It has a plugin system which allows more functionality to be added to it. It has a wonderful community around it who are incredibly helpful. You can find their [forums here](https://forum.obsidian.md/). The application is constantly being improved by the developers with new releases coming out every couple of weeks. Before you know it you'll be heading down the rabbit hole of [building a second brain](https://fortelabs.co/blog/basboverview/) and [Zettlekasten](https://zettelkasten.de/posts/overview/) like I have. Take a look at Obsidian if you're considering levelling up your note taking system and [let me know what you think](https://www.marclittlemore.com/contact/).

[Francesco D'Alessio](https://twitter.com/francescod_ales) from [Keep Productive](https://www.keepproductive.com/) has a great getting started guide for Obsidian:

<iframe title="Embedded YouTube video" src="https://www.youtube-nocookie.com/embed/bLoukY64gYk" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen="" width="100%" height="100%" frameborder="0"></iframe>

[Nick Milo](https://twitter.com/nickmilo) has a really helpful set of videos on using Obsidian. He also has a course called [Linking Your Thinking](https://www.linkingyourthinking.com/) which helps you to build your own personal knowledge management system.

<iframe title="Embedded YouTube video" src="https://www.youtube-nocookie.com/embed/QgbLb6QCK88" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen="" width="100%" height="100%" frameborder="0"></iframe>

[Justin DiRose](https://twitter.com/justindirose) from [Effective Remote Working](https://effectiveremotework.com/) has a great set of videos on learning Obsidian too so start here:

<iframe title="Embedded YouTube video" src="https://www.youtube-nocookie.com/embed/LDPrRQFgJxI" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen="" width="100%" height="100%" frameborder="0"></iframe>

![Marc Littlemore avatar](https://www.gravatar.com/avatar/ad0a50c9f07ddfc34da340c95c5569da?d=mp&s=150 "Marc Littlemore")

**I'm Marc Littlemore.** I’m a Software Engineering Manager who loves to help developers to build quality software.

I can help you to learn more about software testing and intentional remote work.

[Find out more](https://www.marclittlemore.com/about/)

### Likes

 [![](https://webmention.io/avatar/pbs.twimg.com/03170a22c39696f226a502d6297bb8f67b313af6fb142efe5a01e673d83c43d4.jpg "Nick Milo")](https://twitter.com/NickMilo) 

### Replies

Thanks! I'm loving the videos already.[](http://www.marclittlemore.com/bio)[](https://twitter.com/MarcLittlemore)[](https://twitter.com/NickMilo)[](https://twitter.com/obsdmd)

No problem. Would love to hear how you're using it too. I've found it really valuable to link people and ideas together when I'm trying to write. I need to get better at collating the ideas once I record them though.[](https://twitter.com/NickMilo)[](https://twitter.com/obsdmd)[](https://twitter.com/thebrianto)

For now just getting a hang of markup. Alongside researching more into the workflow and overall system of my notes before commiting to just linking randomly. You wouldn't have a flow chart would you ? 😂[](http://www.marclittlemore.com/bio)[](https://twitter.com/MarcLittlemore)[](https://twitter.com/NickMilo)[](https://twitter.com/obsdmd)

Have you seen Nick’s Linking Your Thinking toolkit vault? It’s helpful to explore that. He has the idea of Maps of Content (MOC) that act as index pages. Worth taking a look at. [forum.obsidian.md/t/lyt-kit-now-…](https://forum.obsidian.md/t/lyt-kit-now-downloadable/390)[](https://twitter.com/NickMilo)[](https://twitter.com/obsdmd)[](https://twitter.com/thebrianto)