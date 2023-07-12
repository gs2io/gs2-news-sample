[⇒日本語のREADMEへ](README.md)

# gs2-news-sample

Sample content used to distribute announcements on GS2-News.

## Introduction

Articles distributed by GS2-News are managed using [Hugo](https://gohugo.io/).
There are two ways to create content using Hugo: HTML and Markdown.

It is recommended that you use Markdown to write the content of your articles, leaving the decoration and markup to the template.
This makes it easier to change the design and easier to manage the article data by keeping it to the essential content.

Hugo can build pages very quickly. Depending on the machine specs, this sample page can be built in 10 to 100 ms.
With this high speed, GS2-News is able to build all patterns in advance, even if there is content that shows or hides articles in conjunction with GS2-Schedule event periods.
All patterns are built and deployed in advance.
Then, GS2-News delivers the content fast and safely by granting clients access to the appropriate URLs according to the event status of GS2-Schedule.

## How to check if it works

Please refer to [Hugo official website](https://gohugo.io/) to install Hugo and download the executable binary.

In the root of the downloaded template directory, type

```shell script
hugo server
````

in the root of the downloaded template directory to start the local server.
The URL to access the execution result of the command will be displayed, so please access it from your browser.
If the contents are displayed, the local server has been started successfully.

Hugo supports hot reloading, and if you update article data while the local server is running
Hugo supports hot reloading, so if you update article data while the local server is running, the content will be rebuilt without restarting the local server, and the result of the update will be reflected in the browser.

## Directory structure

Hugo has various functions, but this explanation is limited to the minimum knowledge required to distribute announcement articles on GS2-News.
If you want to know more details, please refer to the documentation on the [Hugo official website](https://gohugo.io/).

### Article data

All article data is stored in `/content`.
Directly under `/content` is a hierarchy of article sections (categories).

In this sample, the
- news
- events
- maintenance
The hierarchy below `/content` contains three types of sections: news, events, and maintenance.

The directory for content (article data) is located further down the hierarchy.
Hugo's original specification allows for content to be placed directly under a section, but GS2-News is not designed to delete articles that do not meet the requirements for publication,
GS2-News has been designed to dig down one directory level for each content so that when deleting an article that does not meet the requirements for publication, it is only necessary to delete the directory.
GS2-News `specifies' that each content directory should be one level down.

It is also possible to place multiple pages of data under the content directory,
You can also place images and other data under the content directory and refer to them from the page.

In the sample, `news/released` is used to place images in articles and to apply custom CSS for each page.

See [Supported Content Formats | Hugo](https://gohugo.io/content-management/formats/) for Markdown notation.

### Front Matter

Content built with Hugo requires a metadata called Front Matter to be described at the beginning of the data.
Front Matter can be described in TOML/YAML/JSON format, but GS2-News `specifies` that Front Matter should be described in YAML.

```yaml
title: "記事の見出し"
date: 2019-11-07T15:20:29+09:00
```

GS2-News requires that the title and date be specified as required fields.
These are also the data that can be retrieved when using the API to retrieve article data.

```yaml
x_gs2_scheduleEventId: grn:gs2:{region}:{ownerId}:schedule:schedule-0001:event:event-0001
```

GS2-News can display announcements only during the event period in GS2-Schedule.
By specifying the event GRN of GS2-Schedule in the above format in Front Matter, you can specify the event to be linked.

### How to display announcements

GS2-News provides two ways to display announcements in-game.
Both are displayed using a browser, but one is to refer to local data, and the other is to retrieve the data from the server.

To view local data, you can download a ZIP file of the entire article from GS2-News.
Using this method, if there are no changes to the article data or template, the previously downloaded data can be used as a cache.
This will ultimately have the effect of reducing the data delivery capacity, which in turn will lead to a reduction in GS2 usage fees.
The download URL of a ZIP file consisting of article data that currently meets the publication requirements can be obtained via the API.

When retrieving from the server data on a case-by-case basis, there is no need to consider the survival period of local data.
Instead, GS2-News manages access to the announcement data distributed by GS2-News not only at the time of ZIP download, but also at the time of each transition to the announcement page.
Instead, GS2-News will need to manage access to the announcement data not only when downloading the ZIP file, but also each time the user moves to the announcement page.
There is no additional cost associated with this, but in order to achieve this, it is necessary to set cookies in the browser used within the application.
The contents that should be set in the cookie and the URL of the page can be obtained via the API.

### Article List Display

When you build content with Hugo, a content list page is automatically created.

For example
```yaml
http://localhost:1313/
````
will display a list of content for all sections.
The order of display is sorted by the time set in the Front Matter of the content, with the newest articles at the top.

If you wish to manipulate this order arbitrarily, you can change the order by setting the weight parameter to the Front Matter.
In fact, in this sample, `news/released` is set to display the oldest articles absolutely at the top.

In addition, the list page of content filtered into sections can also be set to

```yaml
http://localhost:1313/news/
http://localhost:1313/events/
http://localhost:1313/maintenance/
````

The following files are created in ```yaml`` and ``yaml`` respectively.

### Changing the layout and style

Templates for article data can be found in `/layouts`.

#### baseof.html

Defines a common design for all pages.
It can be used to add headers, insert copywrites at the bottom, etc.

#### index.html

Used to display a list of articles in all sections.

#### list.html

Used to display the list of articles.

#### single.html

Used to display the article detail page.

### Notes.

Do not specify `baseURL` in `config.toml`.
GS2-News will automatically set it when building the article data.