# code

## Project setup
```
This works with:
nodejs - v14.5.0
npm    - v6.14.6

npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Customize configuration
This Vue app has 3 components.

DropdownFilter, it can receive a "withFilter" param to add a input search,
without it it will display a regular list.

InputNumber, it receives a "type" param, it can be currency, integer or decimal
currency will convert the number to USD currency, integer will convert any number
with decimals to their nearest integer number, and decimal allow to use digits after the .

InputWordCount, receives a max value, this will define how long the max length can be.
