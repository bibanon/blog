---
layout: post
title: Yotsuba Society Website: Migrating to Drupal From Jekyll
excerpt: "A long story about how we exported data from a rotting Drupal 7.x website, and converted it to Jekyll."
tags: [blog, yotsubasociety, scripting, website]
category: basc
modified: 2015-03-21 14:01:45
---
[The Bibliotheca Anonoma](http://github.com/bibanon/bibanon/wiki) has put together a brand new, [Jekyll](http://jekyllrb.com/) (and [Minimal Mistakes](http://mmistakes.github.io/minimal-mistakes))-based website for the [Yotsuba Society](http://yotsubasociety.org), which beats the rotting Drupal 7.x CMS it used to use. 

And don't worry, all the old pagelinks and downloads still work (we worked hard to make that possible), so no need to change your bookmarks.

## Situation

The [Yotsuba Society](http://www.yotsubasociety.org), recently discovered that their website was getting spam sites injected into their aging Drupal 7.x installation. 

Yes, with these messy, ancient CMS codebases, if you fail to update, security is like swiss cheese. It's a matter of time before your site gets exploited. And even if you think your site has nothing of value, it has valuable server space that spam sites can plaster their junk into.

![Imgur](http://i.imgur.com/j8Klw8T.png)

The admin's original plan was simply to shut down the site indefinitely, but that would have meant the total death of the Yotsuba Society. A great example that sometimes, even the archivists have to archived. So I instead recommended that they abandon the CMS system entirely, and replace it with a Static HTML engine (such as Jekyll). 

The CMS is often a necessary evil in large, account-based websites which implement forums, rich content, multiple posters, and such. However, by now the Yotsuba Society had significantly reduced it's operations, and was only using the CMS as a blog. Because Drupal 7 was now legacy code, it was starting to get spam ads injected into it to trick some poor saps.

Not everything has to be a giant Wordpress/Drupal/Joomla site. If you're only pushing information in a blog and some pages, perhaps with a few Disqus comments on the side, all you need is a static HTML engine, such as [Jekyll](http://jekyllrb.com/).

* **No more complex interfaces to wrestle with.** Just write a page and push (via FTP or Git).
* **No more bloated WYSIYWG editors.** Just write in Markdown syntax, it's easy to learn and has massive potential. Dump YouTube embed codes in, dump internal image links, and be done with it.
* **No more security issues.** Bloated CMS systems, with their accounts and SQL injection vulnerabilities, offer a massive attack surface, especially if it's not well updated. With Static HTML, there's nothing for attackers to attack, other than the web server admin console.
* **Static HTML uses significantly fewer resources.** You don't need a PHP engine or SQL Database to generate pages on the fly anymore. The HTML pages are pregenerated on the author's computer, and just thrown at the viewer. In fact, you can serve them for free via Github Pages (which allows you to use your same ol' custom domains).

And yes, Static HTML engines support comments too, via [Disqus.](https://help.disqus.com/customer/portal/articles/472138-jekyll-installation-instructions)

## Migration Rules

First, we must lay down some ground rules for website migration.

* **NEVER cause link rot.** - Isn't it annoying when your bookmarked links no longer work? Even if the original content has been imported, you will break links if you change them to a different URL structure. Link rot causes a whole host of issues, from reduced accessibility to reduced Google listing presence.
  * We have to make sure that links such as `http://yotsubasociety.org/node/1` still work, or at least redirect in the new site. Luckily, Jekyll has [a `permalink:` option to set custom URLs](http://davidtuite.com/posts/how-to-manage-permalinks-in-jekyll), and a [Jekyll Redirect-from plugin](https://help.github.com/articles/redirects-on-github-pages/) to create multiple redirects. Say goodbye to link rot.
* **Make sure all downloadable files remain in the same location.** - Related to link rot. Ensure that any downloadable files can be accessed from the exact same URLs as you used to use.
* **Retain as much functionality as possible.** - Don't drop features that the old site used to have: no matter how trivial, someone out there really does use them. Try to find some kind of alternative that the new site can provide. 
  * **RSS Feeds** - Yes, there are people out there that still use these, despite that Google Reader is long gone. How else would you subscribe to of blogs? Thankfully, most Jekyll themes come with a templatable `feeds.xml` that generates an RSS XML feed without any effort. 
  * **Comments** - While the Yotsuba Society doesn't have comments, I would have used Disqus if it did, and imported the old comments with all history.
  * **Multiple Authorship** - This is a tricky one, given that it would be unsafe to give account access to all authors of the Yotsuba Society website. Since Jekyll is static HTML and best used with Git anyway, I would migrate the site to Github Pages, where you can give multiple users commit access to a repository.
* **Teach the Website Owners well.** - Make sure that the owners of the site actually know how to use the engine, and provide plenty of documentation.
  * Jekyll is pretty simple to work with, but the actual command-line tools that help create posts can be a pain to install on Windows. Unfortunately, since most of the world uses Windows, you'll have to figure out a suitable alternative.
  * The [Github Online Repo Editor](https://github.com/blog/905-edit-like-an-ace) is actually quite nice, and [you can even create files using it.](https://github.com/blog/1327-creating-files-on-github)
  * We then [created a README.md file](https://github.com/yotsubasociety/yotsubasociety.github.io#getting-started) that provides full documentation on how to create blog posts, edit articles, and modify the site structure.

## Importing a Drupal Website into Jekyll

Jekyll provides a fully-functional [blog import system](http://import.jekyllrb.com/docs/home/) that will grab all pages, tags, categories from Drupal, by communicating with the SQL server. 

> **Note:** This script can't grab image nodes or comments! Remember to grab those manually or obtain via some other method!  
> Also, it only grabs blog posts. Home Pages and other special nodes also need to be obtained manually.

1. Install Ruby: [Mac OS X](https://gorails.com/setup/osx/10.10-yosemite) - [Linux](https://www.ruby-lang.org/en/documentation/installation/)
2. Install Node.js: [Mac OS X](http://nodejs.org/download/) - [Linux](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager)
2. Open a command line.
3. [Install Jekyll-Import.](http://import.jekyllrb.com/docs/installation/) Some manual installation may be necessary.
  * NOTE: This converter requires Sequel and the MySQL gems. 
  * MySQL has been superseded by MariaDB in Linux. Make sure to install `mariadb` and `libmariadbclient-dev` (for Debian)
  * The MySQL gem can be difficult to install on OS X. Make sure to install MySQL, and use this argument instead: `--with-mysql-config=/usr/local/mysql/bin/mysql_config`

```
$ sudo gem install sequel
$ sudo gem install execjs
$ sudo gem install mysql --with-mysql-config=/usr/bin/mysql_config
```

4. Use the import commands shown on Jekyll-Import documentation: [Drupal 6](http://import.jekyllrb.com/docs/drupal6/) - [Drupal 7](http://import.jekyllrb.com/docs/drupal7/)

You should edit the fields in the command before using it.

* `dbname` - The name of your database, just as Drupal asked you during installation. (check PHPMyAdmin)
* `user` - The username of the database user. Check your hosting provider's admin console.
* `password` - The password of the database user.
* `host` - The SQL Server hosting all the website data.
* `prefix` - The table prefixes used in the database. Usually, it's `drupal_`, but you might have set something different.

There are two ways to work with the Drupal SQL Database:

1. You can communicate directly with the curren website's Drupal SQL Database. However, it might be necessary to add your current IP as a trusted host (see your hosting provider for more details).
2. Alternatively, you can make a SQL database dump using PHPMyAdmin, import it, and host your own database on your computer. The `host` entry should then be `localhost`. This can be a bit more challenging to set up, but it reduces security vulnerabilities, and is necessary when a backup is all you've got.

For example, here is the command we used to communicate with the Yotsuba Society MySQL Database:

```bash
$ ruby -rubygems -e 'require "jekyll-import";
    JekyllImport::Importers::Drupal7.run({
      "dbname"   => "yotsubasociety",
      "user"     => "yotsubasociety",
      "password" => "put_your_password_here",
      "host"     => "mysql.yotsubasociety.org",
      "prefix"   => "drupal_"
    })'
```

### Set up a SQL Backup and Export from it

Sometimes you might only have access to a SQL database backup. To export this database, you will have to create a local MySQL/MariaDB server, and import the database backup.

1. Install MySQL/MariaDB. Some Linux distros may have already migrated to MariaDB, which is a drop-in replacement for MySQL.
2. Start the MySQLd Service. On Debian/Ubuntu:

    sudo service start mysqld

3. Run the MySQL/MariaDB Secure Installation Script as root, and follow the instructions. Make sure to drop the test database and reload the privilege tables.

    sudo mysql_secure_installation

4. Once you're finished, log in as the root MySQL user.

    mysql -u root -p

5. Create a user to manage the databases. For example, I used user: `localuser` with password: `password`.

```
MariaDB [(none)]> CREATE USER 'localuser'@'localhost' IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> GRANT ALL PRIVILEGES ON *.* TO 'localuser'@'localhost'
    ->            WITH GRANT OPTION;
Query OK, 0 rows affected (0.00 sec)

```

6. Now create a new database to import the backup into. In this example, we call it `yotsubasociety`. Then quit.

```
MariaDB [(none)]> create database yotsubasociety;
Query OK, 1 row affected (0.00 sec)

MariaDB [(none)]> quit
```

Now you can import the database. As the `localuser` user, we access the `localhost` SQL server and insert the backup.sql dump into the `yotsubasociety` database.

    mysql -u localuser -p -h localhost yotsubasociety < yotsubasociety352015.sql

Finally, here's the command we used to export to Jekyll from the `localhost` SQL Server. Usually, the prefix is `drupal_`.

```bash
$ ruby -rubygems -e 'require "jekyll-import";
    JekyllImport::Importers::Drupal7.run({
      "dbname"   => "yotsubasociety",
      "user"     => "localuser",
      "password" => "password",
      "host"     => "localhost",
      "prefix"   => "drupal_"
    })'
```

### Cleaning up the Posts

Unfortunately, [due to bugs in the Drupal import script:](https://github.com/jekyll/jekyll-import/issues/90)

* The author names aren't imported.
* The correct slugs aren't imported. In order to maintain compatibility with the old site's links, we need to grab the `url_alias` table as shown in [the Drupal 7 database schema.](https://www.drupal.org/files/er_db_schema_drupal_7.png) 
* The title is corrupted, as shown below:

```
---
layout: post
title: !binary |-
  SW50cm9kdWN0aW9uIHRvIFlvdHN1YmEgU29jaWV0eQ==
created: 1300717398
---
```

On the other hand, the correct title (well, lost underscores and Capitals) is always posted in the filename:

`2014-03-04-the-complete-history-of-4chan.md`

There must be some way we can make the title work. (though maybe it might actually display when compiled?)

### Patching Jekyll-Import to work with Drupal 7

Jekyll-Import's Drupal 7 import scripts just aren't perfect or complete, [as this bug report shows.](https://github.com/jekyll/jekyll-import/issues/90) We had to patch it to give us the correct title.

I have fixed this bug entirely by forcing the strings into Unicode format (and stripping any preceding and ending whitespace with `strip`):

```
# Get the relevant fields as a hash, delete empty fields and convert
# to YAML for the header
data = {
	'layout' => 'post',
	'title' => title.strip.force_encoding("UTF-8"),
	'created' => created,
	'excerpt' => summary
}.delete_if { |k,v| v.nil? || v == ''}.to_yaml
```

That way, it works even for those pesky titles with stray \xE2 junk that screws everything up.

### Hacking the Jekyll-Import Gem

Before the change went live at the [Jekyll-Import](https://github.com/jekyll/jekyll-import/blob/master/lib/jekyll-import/importers/drupal7.rb#L76) gem, I had to push the change to my existing gem to make the title work. First, we will have to be able to edit the gem, which is non-trivial. Thankfully, this user on StackOverflow has a handy [Fork and Source method.](http://stackoverflow.com/a/8690945)

1. Fork the [Jekyll-Import](https://github.com/jekyll/jekyll-import/) script to your own Github account.
2. Make your edits to [the Drupal import script](https://github.com/antonizoon/jekyll-import/blob/master/lib/jekyll-import/importers/drupal7.rb#L76) on your own repository. For example, I edited the following to add a `permalink` entry with spaces as underscores, and a better Title section:

          # Get the relevant fields as a hash, delete empty fields and convert
          # to YAML for the header
          data = {
            'layout' => 'post',
            'title' => '' + title,
            'permalink' => '/' + title.strip.gsub!(' ', '_'),
            'created' => created,
            'excerpt' => summary
          }.delete_if { |k,v| v.nil? || v == ''}.to_yaml

3. Now, clone your [forked Jekyll-Import repository](https://github.com/antonizoon/jekyll-import/) and add this to the Gemfile (also remove the `gemspec` line):

       gem 'gem_name', :git => 'git://github.com/username/foo'

4. Run the command `bundle install` to install the modified Gem.
5. Run the Jekyll-Import command you planned to use as usual. If it works, submit a pull request to the authors.

(though for me, the changes didn't stick, so I just hacked the file directly at: `/usr/lib/ruby/gems/1.9.1/gems/jekyll-import-0.5.3/lib/jekyll-import/importers/drupal7.rb`)

### Obtain url_alias

If your website used a `url_alias`, as it probably should have, you will definitely want to inject them into the blog pages as a Jekyll `permalink`. 

Sadly, [as you see from the Drupal 7 Schema](https://www.drupal.org/files/er_db_schema_drupal_7.png), the `url_alias` table is in no way relationally related to a node, which is just plain stupid and makes life hard.

I guess the only way to insert this right is to do it by hand. That's what we were forced to do.

First, use the following MySQL command to open the table and grab everything from the `url_alias` table.

```
MariaDB [(none)]> USE yotsubasociety
Database changed
MariaDB [yotsubasociety]> SELECT * FROM drupal_url_alias;
```

You will obtain all the `url_alias` from the database:

| pid | source  | alias                                                    |
|----:|:-------:|:---------------------------------------------------------|
|   1 | [node/4](http://www.yotsubasociety.org/node/4)  | articles |
|   2 | [node/6](http://www.yotsubasociety.org/node/6)  | the_otaku_continuum |
|   3 | [node/7](http://www.yotsubasociety.org/node/7)  | contact |
|   4 | [node/8](http://www.yotsubasociety.org/node/8)  | imageboardguide |
|   5 | [node/9](http://www.yotsubasociety.org/node/9)  | boardsfaq |
|   6 | [node/10](http://www.yotsubasociety.org/node/10) | Channerstages |
|   7 | [node/5](http://www.yotsubasociety.org/node/5)  | staff_recruitment_and_content_drive |
|   9 | [node/12](http://www.yotsubasociety.org/node/12) | archiveslaunch |
|  10 | [node/13](http://www.yotsubasociety.org/node/13) | An_Ode_to_the_F40PH |
|  11 | [node/14](http://www.yotsubasociety.org/node/14) | news_update_5_23_2011 |
|  12 | [node/15](http://www.yotsubasociety.org/node/15) | Critique_and_Commentary |
|  13 | [node/16](http://www.yotsubasociety.org/node/16) | What_is_Chanthropology |
|  14 | [node/17](http://www.yotsubasociety.org/node/17) | originsofanon_pti |
|  15 | [node/18](http://www.yotsubasociety.org/node/18) | originsofanon_ptii |
|  16 | [node/19](http://www.yotsubasociety.org/node/19) | originsofanon_ptiii |
|  17 | [node/20](http://www.yotsubasociety.org/node/20) | 20084chanthreads |
|  18 | [node/21](http://www.yotsubasociety.org/node/21) | yotsuba_archiver_changeover |
|  19 | [node/22](http://www.yotsubasociety.org/node/22) | the2ndchannel_pti |
|  20 | [node/23](http://www.yotsubasociety.org/node/23) | Raids_and_invasions_briefing |
|  21 | [node/24](http://www.yotsubasociety.org/node/24) | eternal_summer |
|  22 | [node/25](http://www.yotsubasociety.org/node/25) | Classic_4chan_memes |
|  23 | [node/26](http://www.yotsubasociety.org/node/26) | YS_Staff |
|  24 | [node/27](http://www.yotsubasociety.org/node/27) | Briefings |
|  25 | [node/28](http://www.yotsubasociety.org/node/28) | Futaba_Channel_Briefing |
|  26 | [node/29](http://www.yotsubasociety.org/node/29) | 2channel_briefing |
|  27 | [node/30](http://www.yotsubasociety.org/node/30) | material_submission |
|  28 | [node/31](http://www.yotsubasociety.org/node/31) | Join_us |
|  29 | [node/32](http://www.yotsubasociety.org/node/32) | How_to_Easily_Archive_a_Web_Site |
|  30 | [node/33](http://www.yotsubasociety.org/node/33) | Television_Mostly_Sucks |
|  31 | [node/34](http://www.yotsubasociety.org/node/34) | briefing_channers_age |
|  32 | [node/35](http://www.yotsubasociety.org/node/35) | YSArchives_Update10_10_2011 |
|  33 | [node/36](http://www.yotsubasociety.org/node/36) | burial_of_Illusion_of_rules_one_and_two |
|  34 | [node/37](http://www.yotsubasociety.org/node/37) | TheSecondChannel |
|  35 | [node/38](http://www.yotsubasociety.org/node/38) | TheThirdChannel |
|  36 | [node/39](http://www.yotsubasociety.org/node/39) | news_update-10-30-2011 |
|  37 | [node/40](http://www.yotsubasociety.org/node/40) | re-savetheinternet |
|  38 | [node/41](http://www.yotsubasociety.org/node/41) | like_fish |
|  39 | [node/42](http://www.yotsubasociety.org/node/42) | Report_of_Joint_Operations |
|  40 | [node/43](http://www.yotsubasociety.org/node/43) | 2011_Diaspora |
|  41 | [node/44](http://www.yotsubasociety.org/node/44) | americawonet |
|  42 | [node/45](http://www.yotsubasociety.org/node/45) | how_to_lurk_moar |
|  43 | [node/46](http://www.yotsubasociety.org/node/46) | Alternatives_to_Scared_Straight |
|  44 | [node/47](http://www.yotsubasociety.org/node/47) | chanverse_racism_briefing |
|  45 | [node/48](http://www.yotsubasociety.org/node/48) | 1-26-2012_pressrelease |
|  46 | [node/49](http://www.yotsubasociety.org/node/49) | dealing_with_depression |
|  47 | [node/50](http://www.yotsubasociety.org/node/50) | Can_you_make_a_emblem |
|  48 | [node/51](http://www.yotsubasociety.org/node/51) | If_anyone_cares |
|  49 | [node/52](http://www.yotsubasociety.org/node/52) | sp_superbowl2012 |
|  50 | [node/53](http://www.yotsubasociety.org/node/53) | ruoccupy |
|  51 | [node/54](http://www.yotsubasociety.org/node/54) | Nurse-kun_archives |
|  52 | [node/55](http://www.yotsubasociety.org/node/55) | facesofanonpti |
|  53 | [node/56](http://www.yotsubasociety.org/node/56) | facesofanonptii |
|  54 | [node/57](http://www.yotsubasociety.org/node/57) | YSchan_first_year |
|  55 | [node/59](http://www.yotsubasociety.org/node/59) | wikichan_restoration_complete |
|  56 | [node/60](http://www.yotsubasociety.org/node/60) | YSArchives_Update_5_6_2012 |
|  57 | [node/61](http://www.yotsubasociety.org/node/61) | Computer games keep me mentally active |
|  58 | [node/62](http://www.yotsubasociety.org/node/62) | tuesday is backup day |
|  59 | [node/63](http://www.yotsubasociety.org/node/63) | 2012Hibernation |
|  60 | [node/64](http://www.yotsubasociety.org/node/64) | Mostly_Perceived_Fear_and_Loathing_of_Reddit |
|  61 | [node/65](http://www.yotsubasociety.org/node/65) | Some_Chinese_Rail_Pics |
|  62 | [node/66](http://www.yotsubasociety.org/node/66) | two_panels-two_powerpoints |
|  63 | [node/67](http://www.yotsubasociety.org/node/67) | The_Imageboard_World_2 |
|  64 | [node/68](http://www.yotsubasociety.org/node/68) | imageboardworld2_present |
|  65 | [node/69](http://www.yotsubasociety.org/node/69) | imageboardworld2_video |
|  66 | [node/70](http://www.yotsubasociety.org/node/70) | Links and Resources |
|  67 | [node/71](http://www.yotsubasociety.org/node/71) | russian_chanverse_notes |
|  68 | [node/72](http://www.yotsubasociety.org/node/72) | lurkmore_to_briefing |
|  69 | [node/73](http://www.yotsubasociety.org/node/73) | The_da_1chan_ru_came |
|  70 | [node/74](http://www.yotsubasociety.org/node/74) | the_russian_chanverse_part_2 |
|  71 | [node/75](http://www.yotsubasociety.org/node/75) | savetheinternet_public_advisory |
|  72 | [node/76](http://www.yotsubasociety.org/node/76) | chanverse_review |
|  73 | [node/77](http://www.yotsubasociety.org/node/77) | two_major_updates |
|  74 | [node/78](http://www.yotsubasociety.org/node/78) | Routine_archival_ceased |
|  75 | [node/79](http://www.yotsubasociety.org/node/79) | 19393370_hallo_I'm_from_Futaba |
|  76 | [node/80](http://www.yotsubasociety.org/node/80) | RIP_Yotsuba_Society_Archives |
|  77 | [node/81](http://www.yotsubasociety.org/node/81) | YSArchives_4chan |
|  78 | [node/82](http://www.yotsubasociety.org/node/82) | 4chan_YSArchives_inducted_to_Stanford_Digital_Repository |
|  79 | [node/83](http://www.yotsubasociety.org/node/83) | YS_panel_presentations |
|  80 | [node/84](http://www.yotsubasociety.org/node/84) | The_Complete_History_of_4chan |
|  81 | [node/85](http://www.yotsubasociety.org/node/85) | Basic_Glossary_of_the_Chanverse |
|  82 | [node/86](http://www.yotsubasociety.org/node/86) | The_Silent_WebM_revolution |
|  83 | [node/87](http://www.yotsubasociety.org/node/87) | 2channel_Japanese_Wiki_translation |

I created this nice markdown table by taking the MySQL Table SELECT query, and changing the middle bar to Markdown format (changed all the `+` signs to `|`). 

To ease the process of matching a node to a shortlink, I used a regex in the KDE Kate editor to match all `node/##` instances, and convert them into markdown links to the site in question. This way, I can just click a link as shown above to find the original title.

* **Find:** `node/(\d+)`
* **Replace:** `[node/\1](http://www.yotsubasociety.org/node/\1)`

### Inject url_alias

Then, I open every single post dumped, and add `permalink: /page_name_to_use` in the YAML section. This can take a while.

I also use the handy [Jekyll Redirect-from](https://github.com/jekyll/jekyll-redirect-from) gem to make the `/node/##` links work as well:

```
redirect_from:
  - /node/1/
  - /Page_Name_In_Underscores/
```

> **Note:** Make sure that the links all end in a `/`. This way, a folder with `/node/1/index.html` will be created, instead of `/node/1`, which is just spit at the browser as a downloadable binary. 

### Choose a Jekyll Theme

Now that you have the data exported, choose a Jekyll theme that fits for you. Here's a nice listing of some [free, amazing themes.](http://jekyllthemes.org/)

Since I needed something simple, and since the [Bibliotheca Anonoma Blog](http://blog.bibanon.org) and [my own blog](http://archivis.me) uses the same theme, I used the very nice, simple, and responsive [Minimal Mistakes](https://github.com/mmistakes/minimal-mistakes) theme.

All I had to do was `git clone` the theme, paste all the pages into the theme, and do a bit of configuration, and the Jekyll site is ready.

### Hosting the Jekyll Site

For the most part, you will want to host your Jekyll Site using Github Pages. Github has the best support for Jekyll (they created it), and offers free limitless Static HTML hosting.

Just push your website to the `master` branch on your `username.github.io` repository, or to the `gh-pages` branch on any random Github repo. Github will automatically generate the website at `username.github.io` or `username.github.io/repository`.

For the Yotsuba Society, we had them create a new Github Organization (which is free), whereby we pushed the Jekyll website to the `master` branch on the repo: `yotsubasociety.github.io`. It was then instantly hosted at `http://yotsubasociety.github.io` without a hitch.

Notice that you can also use custom domains with Github Pages! We had to direct `http://yotsubasociety.org` to the Github Pages server, [following the instructions provided by Github.](https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages/)

And now, the new Yotsuba Society Website is complete. What an amazing, difficult, but eye-opening journey.
