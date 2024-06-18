# Animixplay Full API

An unofficial [Animixplay](https://animixplay.fun). Supports Node.js and browsers.

```bash
npm install Animixplay-full-api@6.0.0-beta.0
```

```javascript
// Demo
Anime.search({
    title: 'One Piece',
    limit: Infinity, // API Max is 100 per request, but this function accepts more
    hasAvailableChapters: true,
}).then((Animes) => {
    console.log('There are', Animes.length, 'Animes with One Piece in the title!');
    Animes.forEach((Anime) => {
        console.log(Anime.localTitle);
    });
});
```

#### Authentication

To login with a personal account

```javascript
await loginPersonal({
    clientId: 'id',
    clientSecret: 'secret',
    password: 'password',
    username: 'username',
});
```

#### Page Images

```javascript
// Get a Anime with chapters
const Anime = await Anime.getByQuery({ order: { followedCount: 'desc' }, availableTranslatedLanguage: ['en'] });
if (!Anime) throw new Error('No Anime found!');

// This will get the first English chapter for this Anime
const chapters = await Anime.getFeed({ translatedLanguage: ['en'], limit: 1 });
const chapter = chapters[0];

// Get the image URLs for this chapter
const pages = await chapter.getReadablePages();
console.log(pages);
```

#### Relationships

Animixplay will return Relationship objects for associated data objects instead of the entirety of each object.

For example, authors and artists will be returned as `Relationship<Author>` when requesting a [Anime](https://kayoanimetv.com/). To request the author data, use the `resolve` method.

Additionally, you can supply `'author'` to the `includes` parameter to include the author data alongside the Anime request. You will still need to call the `resolve` method, but the promise will return instantly.

```javascript
const Anime = await Anime.getRandom();
const firstAuthor = await Anime.authors[0].resolve();
console.log('The first author is', firstAuthor.name);

// Use resolveArray to resolve an array of Relationships more efficiently
const allAuthors = await resolveArray(Anime.authors);
console.log('All authors are', allAuthors.map((a) => a.name).join(', '));

// Because authors are included, the author relationships are cached
const otherAnime = await Anime.getByQuery({ includes: ['author'] });
if (otherAnime) {
    console.log('The authors for this Anime are included and cached:', otherAnime.authors[0].cached);
    const author = await otherAnime.authors[0].resolve();
    console.log('The author is', author.name);
}
```

#### Finding Anime

The most common way to find Anime is to use the `search` method. However, there are a few other ways as well:

```javascript
// Basic Search
Anime.search({
    title: 'One Piece',
    limit: Infinity, // API Max is 100 per request, but this function accepts more
    hasAvailableChapters: true,
}).then((Animes) => {
    console.log('There are', Animes.length, 'Animes with One Piece in the title!');
    Animes.forEach((Anime) => {
        console.log(Anime.localTitle);
    });
});

// This will return the first result from a search
Anime.getByQuery({ title: 'One Piece' }).then((Anime) => {
    if (Anime) console.log('Found a One Piece Anime with id', Anime.id);
    else console.log('No One Piece Anime found');
});

// You can get a random Anime
Anime.getRandom().then((Anime) => console.log('Random:', Anime.localTitle));

// You can get Anime directly by UUID
Anime.get('Anime-uuid-here').then((Anime) => console.log(Anime));
``
