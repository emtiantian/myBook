``` conf
# your document html and data

location ~* \.(?:manifest|appcache|html?|xml|json)$ {

  expires -1;

}




# Media: images, icons, video, audio, HTC

location ~* \.(?:jpg|jpeg|gif|png|ico|cur|gz|svg|svgz|mp4|ogg|ogv|webm|htc)$ {

  expires 1M;

  access_log off;

  add_header Cache-Control "public";

}



# CSS and Javascript

location ~* \.(?:css|js)$ {

  expires 1y;

  access_log off;

  add_header Cache-Control "public";

}
```