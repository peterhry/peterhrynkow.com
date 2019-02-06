<!--
### Low-level vs High-level components

Buttons, dropdown menus, tiles -- these are examples of low-level components. They don't do anything particularly useful until you combine them with other components and write some logic. 

We are providing low-level components to maximize composition capabilities.
[Material-UI API Design Approach](https://material-ui.com/guides/api/)

Low-level components afford more flexibility because the consumer can combine them in more ways.

But providing the consumer only with small, low-level components doesn't provide much in the way of user experience value.

Is it better to have one component that does five things or five components that each do one thing?

For example, you could create one video component that supports multiple video providers (i.e. YouTube, Vimeo, Twitch) or multiple video components that each support one provider. The former provides a higher-level of abstraction because it hides away details about the video provider.
-->