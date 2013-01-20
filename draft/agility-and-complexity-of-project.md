**UPD:** new article http://ruby-lang.info/blog/code-statistics-and-the-power-law-paretto-80-20-rul-hqo

!![icon] When You estimate the value of new Project one of consideration - how complicated it is? How much time it'll takes to learning, and customize it?

I found a dumb simple way to rough measure it - calculate the size of source codes.
Remember old times when programmer got paid proportionally number of lines he write? Well this is the quite opposite approach - the less code (for similar functionality) the more valuable the project is.

Definitely it's a rough estimation, there are also lots of other things to be considered.
But the biggest advantage of this estimation is speed and easiness - You can instantly estimate hundreds of projects.

Let's look at concrete numbers and measure some of interesting Open Source Projects (lines and characters must be multiplied by 1000).

```
Name                 Files   Lines   Chars
rubinius             6918    653     13280
wordpress            347     166     4372
redmine              703     67      1818
drupal               104     49      1246
teambox              802     39      1032
fat_free_crm         492     25      760
diaspora             633     30      758
actionpack           160     26      723
spree                769     27      703
railscollab          291     17      460
shapado              375     16      451
mongoid              202     15      320
mongo_mapper         156     15      313
refinerycms          306     10      291
active_admin         185     10      288
rucksack             200     9       226
calipso              59      9       195
rad_core             297     8       184
```

Statistics for some Open Source games

```
Name                 Files   Lines   Chars
stratagus            5107    2878    60588
springrts            1916    512     12980
btanks               503     86      9530
glest                311     49      1092
andors_trail         96      11      354
catchemrpg           104     13      347
flare                104     16      330
wrath                219     11      211
rawkets              18      9       202
orona                39      5       125
warpg                17      3       115
fontanero            6       1       32
```

Note, that code size not always proportional to the goodness of game, for example, the springrts is way cooler than stratagus but it's code about 2.5 times smaller.

Note also glest - it's almost as cool as the springrts, but it's code is about 10 times smaller. So, in some sence we can say that the "Price/Value ratio" of Glest is bigger than the SpringRts.

[Here's the script][script] that calculates all this stuff.

[script]: https://github.com/alexeypetrushin/misc/blob/master/text_statistics/code_size.rb
[icon]: https://firenet.s3.amazonaws.com/fs/4da14c38743f0f218d00000f/4da14ea9743f0f218d000015/4e0a42ce743f0f489a000052/tape.thumb.jpg