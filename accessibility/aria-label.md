Tag is simply a text with a colourful background. It has no role unless it is used as a button. Adding `aria-label="tag"` on the element would actually overwrite the text value of that element.  
For example lets consider this case.  

<Tag aria-label="tag">Hello world</Tag>

If I would find this component with a screen reader it would read "tag" instead of "Hello world". So by adding the `aria-label` here we would make the UX worse for the users who use screen readers or in other words the component would become inaccessible.