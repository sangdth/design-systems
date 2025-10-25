Currently we have this file to control all the z-index in our app, as this could lead to problems:
- the old number might now work in new context: say the modal has `100040`, but the dropdown is `1100`, so if we use dropdown inside modal, problem migh happen

research
https://developer.mozilla.org/en-US/docs/web/css/css_positioning/understanding_z_index
https://philipwalton.com/articles/what-no-one-told-you-about-z-index/

https://www.huy.dev/z-index-management-2019-04/ (do not like it)
https://www.smashingmagazine.com/2019/04/z-index-component-based-web-application/