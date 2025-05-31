
```shell
# filter items less than 40
$[?(@ < 40)]

# $ is the root of json content
# ? for checking with ()
# @ is the item


$[?(@ == 40)]
$[?(@ != 40)]
```

```shell
$.car.wheels[?(.location == "rear-right")].model

```