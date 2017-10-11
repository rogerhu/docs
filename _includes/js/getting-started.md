# Getting Started

If you haven't set up your project yet, please [head over to the QuickStart guide](/apps/quickstart#js/native/blank) to get up and running. You can also check out our [API Reference](/docs/js/api/) for more detailed information about our SDK.

The Parse platform provides a complete backend solution for your mobile application. Our goal is to totally eliminate the need for writing server code or maintaining servers.</p>

<div class='tip info'><div>
  If you're looking to build a React application with Parse, we provide a special library for that. All of the documentation is available at the [GitHub repo](https://github.com/ParsePlatform/ParseReact).
</div></div>

Our JavaScript SDK is originally based on the popular [Backbone.js](http://backbonejs.org/) framework, but it provides flexible APIs that allow it to be paired with your favorite JS libraries. Our goal is to minimize configuration and let you quickly start building your JavaScript and HTML5 app on Parse.

Our SDK supports Firefox 23+, Chrome 17+, Safari 5+, and IE 10. IE 9 is supported only for apps that are hosted with HTTPS.

On Parse, you create an App for each of your mobile applications. Each App has its own application id and JavaScript client key that you apply to your web app. Your account on Parse can accommodate multiple Apps. This is useful even if you have one application, since you can deploy different versions for test and production.

Most of the examples below use `success` and `error` callbacks for simplicity. If you're familiar with JavaScript `Promises` or would like to learn how to avoid writing nested code, scroll down to the [Promises](#promises) section.
