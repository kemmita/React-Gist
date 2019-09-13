1. First and slow way
```
sudo npm i -g yarn
```
```
sudo npm i -g live-server
```
```
//to serve app run 
live-server index.html
```
```
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id=""></div>
    <script src="https://unpkg.com/react@16.0.0/umd/react.development.js"></script>
    <script src="https://unpkg.com/react-dom@16.0.0/umd/react-dom.development.js"></script>
</body>
</html>

```
2. fast better way
```
sudo npm i -g npx
```
```
npx create-react-app appnamehere --typescript
```
