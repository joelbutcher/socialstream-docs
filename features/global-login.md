# Global Login

You may have multiple pages where you allow users to authenticate with your application (such as a section at the bottom of your articles in a blog), then you may wish to avoid having to direct users to the `login`, or `registration` pages in order to authenticate them.

If this is the case, you can enable "Global Login" to allow users to authenticate from anywhere on your site. To do this, simply enable the following snippet to the `features` section of your socialstream config:

```php
Features::globalLogin(),
```
