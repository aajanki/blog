# Bittien takana -blogin lähdekoodit

Lue blogia osoitteessa [bittientakana.blog](https://bittientakana.blog)

## Alkutoimenpiteet

```
git submodule init
git submodule update
```

## Uusi blogipostaus

```
hugo new content content/blog/otsikko/index.md
```

## Käännös lähdekoodista HTML:ksi

```
hugo server --buildDrafts
```
