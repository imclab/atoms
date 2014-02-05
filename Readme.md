# List of Atoms

Status: not perfect yet.

## Installation

```bash
npm install atoms-dataset
```

## Notes

Scraped off wikipedia with basic jQuery:

http://en.wikipedia.org/wiki/List_of_elements

```js
function extractHeaders() {
  var headers = [];

  $('table.wikitable thead th').each(function(){
    headers.push($('> a', this).text() || $(this).text());
  });

  return headers;
}

console.log(extractHeaders().join('\n'));
```

which gives you this:

```
Z
Sym
Element
Origin of name[1]
Group
Period
Atomic weightu()
Densitygcm3
MeltK
BoilK
HeatJgK
Neg
Abundancemgkg
```

then I gave them better names (and removed the irrelevant ones):

```js
var headers = [
  'number',
  'symbol',
  null,
  null,
  'period',
  'weight',
  'density',
  'melting-point',
  'boiling-point',
  'heating-point',
  'electronegativity',
  'abundance'
];
```

and extracted the data from the table body:

```js
var headers = [
  'number',
  'symbol',
  null,
  null,
  'period',
  'weight',
  'density',
  'melting-point',
  'boiling-point',
  'heating-point',
  'electronegativity',
  'abundance'
];

function extractBody() {
  var data = [];

  $('table.wikitable tbody tr').each(function(){
    if (!$(this).is(':visible')) return;

    var item = {};

    $('td', this).each(function(i){
      if (!headers[i]) return;

      // remove junk
      $('sup, span:hidden', this).remove();

      // have to do some manual logic here
      switch (headers[i]) {
        case 'density':
          item.density = value($('.sorttext', this).text());
          break;
        default:
          item[headers[i]] = value($(this).text());
          break;
      }
    });

    data.push(item);
  });

  function value(text) {
    text = text.trim();
    if ('–' == text) return undefined;
    if (!text) return undefined;
    return text;
  }

  return data;
}

console.log(JSON.stringify(extractBody(), null, 2));
```

Eventually this code will become a module and will become easier to do the next.

## Licence

MIT