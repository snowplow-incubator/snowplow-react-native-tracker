
# @snowplow/react-native-tracker

[![Early Release]][Tracker Classificiation]
[![Build Status][gh-actions-image]][gh-actions]
[![License][license-image]][license]

[![Release][release-image]][releases]
[![RN version][react-native-v-image]][react-native-v]
[![downloads][downloads-dm-image]][downloads-dm]


Feedback and contributions are welcome - if you have identified a bug, please log an issue on this repo. For all other feedback, discussion or questions please open a thread on our [discourse forum](https://discourse.snowplowanalytics.com/).

## Getting started

From the root of your [React Native][react-native] project:

`$ npm install @snowplow/react-native-tracker --save`

### Instrumentation

To setup, you first need to import the module and then create the tracker object using the `createTracker` function.

Then you can start tracking events using the relevant tracking methods.

```JavaScript
import { createTracker } from '@snowplow/react-native-tracker';

const tracker = createTracker('my-namespace', {
    // required
    endpoint: 'test-url',
    appId: 'my-app-id',

    // optional - defaults shown
    method: 'post',
    protocol: 'https',
    base64Encoded: true,
    platformContext: true,
    applicationContext: false,
    lifecycleEvents: false,
    screenContext: true,
    sessionContext: true,
    foregroundTimeout: 600,
    backgroundTimeout: 300,
    checkInterval: 15,
    installTracking: false,
  });

tracker.trackSelfDescribingEvent(
  {
    schema: 'iglu:com.acme/hello_world_event/jsonschema/1-0-0',
    data: {
      message: 'hello world'
    }
  }
);

```

### Running on iOS

`cd ios && pod install && cd ..`

Run the app with: `react-native run-ios` from the root of the project.

### Running on Android

`react-native run-android` from the root of the project.

## Available tracker methods

All methods take a JSON of named arguments.


### Set the subject data:

Setting custom subject data is optional, can be called any time, and can be called again to update the subject. Once set, the specified parameters are set for all subsequent events. (For example, a userid may be set after login, and once set all subsequent events will contain the userid).

```JavaScript
setSubjectData({ // All parameters optional
  userId: 'my-userId', // user id, string
  screenWidth: 123, // screen width, integer
  screenHeight: 456, // screen height, integer
  colorDepth: 20, // colour depth, integer
  timezone: 'Europe/London', // timezone, string enum
  language: 'en', // language, string enum
  ipAddress: '123.45.67.89', // IP address, string
  useragent: '[some-user-agent-string]', // user agent, string
  networkUserId: '5d79770b-015b-4af8-8c91-b2ed6faf4b1e', // network user id, UUID4 string
  domainUserId: '5d79770b-015b-4af8-8c91-b2ed6faf4b1e',// domain user id, UUID4 string
  viewportWidth: 123, // viewport width, integer
  viewportHeight: 456 // viewport height, integer
  }
);
```


### Track a custom event:

```JavaScript
trackSelfDescribingEvent({ // Self-describing JSON for the custom event
  schema: 'iglu:com.acme/event/jsonschema/1-0-0',
  data: {message: 'hello world'}
});
```

### Track a structured event:

```JavaScript
trackStructuredEvent({
  // required
  category: 'my-category',
  action: 'my-action',
  // optional
  label: 'my-label',
  property: 'my-property',
  value: 50.00
  }
);
```

### Track a Screen View:

Note - for the track Screen View method, if previous screen values aren't set, they should be automatically set by the tracker.

```JavaScript
trackScreenViewEvent({
  screenName: 'my-screen-name', //required
  // optional:
  screenType: 'my-screen-type',
  transitionType: 'my-transition'
  }
);
```

### Track a Page View:

```JavaScript
trackPageViewEvent({
  pageUrl: 'https://www.my-page-url.com', // required
  //optional:
  pageTitle: 'my page title',
  pageReferrer: 'http://www.my-refr.com'
  }
);
```

### Attaching custom contexts

All track methods take an optional second argument - an array of 0 or more self-describing JSONs for custom contexts that will be attached to the event:

```JavaScript

trackSelfDescribingEvent(
  // Self-describing JSON for the custom event
  {
    schema: 'iglu:com.acme/event/jsonschema/1-0-0',
    data: {message: 'hello world'}
  },
  // array of contexts
  [
    {
      schema: 'iglu:com.acme/entity/jsonschema/1-0-0',
      data: {myEntityField: 'hello world'}
    }
  ]
);

trackStructuredEvent(
  // the structured event's properties
  {
    // required
    category: 'my-category',
    action: 'my-action',
    // optional
    label: 'my-label',
    property: 'my-property',
    value: 50.00
  },
  // the contexts' array
  [
    {
      schema: 'iglu:com.acme/entity/jsonschema/1-0-0',
      data: {myEntityField: 'hello world'}
    }
  ]
);

trackScreenViewEvent(
  // the screen_view event's properties
  {
    screenName: 'my-screen-name', //required
    // optional:
    screenType: 'my-screen-type',
    transitionType: 'my-transition'
  },
  // the contexts' array
  [
    {
      schema: 'iglu:com.acme/entity/jsonschema/1-0-0',
      data: {myEntityField: "hello world"}
    }
  ]
);

trackPageViewEvent(
  // the page_view event's properties
  {
    pageUrl: 'https://www.my-page-url.com', //required
    // optional
    pageTitle: 'my page title',
    pageReferrer: 'http://www.my-refr.com'
  },
  // the context's array
  [
    {
      schema: 'iglu:com.acme/entity/jsonschema/1-0-0',
      data: {myEntityField: "hello world"}
    }
  ]
);
```

## Contributing

Please read CONTRIBUTING.md for general guidelines on contributing.

### Maintainer quick start

Assuming a [react-native environment][react-native-environment] is set up, from the root of the repository to launch the DemoApp:

```bash
npm install
npm run compile

cd DemoApp
yarn install

yarn start # to start Metro

```

Then in another terminal:

1. For Android

```bash
cd DemoApp
yarn android
```

2. For iOS

```bash
cd DemoApp/ios && pod install
cd .. && yarn ios
```

### Testing

Currently, testing is done manually. It is recommended to use [Snowplow Micro][snowplow-micro] or a [Snowplow Mini][snowplow-mini] instance to test your changes during development.

During development, to quickly test changes, the `.scripts/quickTest.sh` bash script can be used, assuming the DemoApp has already been built before. This simply replaces relevant files in the node_modules folder, and re-runs react native.

```bash
# android

bash .scripts/quickTest.sh android

# ios

bash .scripts/quickTest.sh ios

# both

bash .scripts/quickTest.sh both
```

The `.scripts/cleanBuildAndRun.sh` script offers a naive way to rebuild the entire project with your changes. It uses `npm pack` to create an npm package locally, and installs it to the DemoApp, then it removes pod and gradle lock files and rebuilds all dependencies.

```bash
# android

bash .scripts/cleanBuildAndRun.sh android

# ios

bash .scripts/cleanBuildAndRun.sh ios

# both

bash .scripts/cleanBuildAndRun.sh both
```

## Find out more

| Technical Docs              |
|-----------------------------|
| ![i1][techdocs-image]       |
| [Technical Docs][techdocs]  |

[Tracker Classificiation]: https://docs.snowplowanalytics.com/docs/collecting-data/collecting-from-own-applications/tracker-maintenance-classification/
[Early Release]: https://img.shields.io/static/v1?style=flat&label=Snowplow&message=Early%20Release&color=014477&labelColor=9ba0aa&logo=data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAMAAAAoLQ9TAAAAeFBMVEVMaXGXANeYANeXANZbAJmXANeUANSQAM+XANeMAMpaAJhZAJeZANiXANaXANaOAM2WANVnAKWXANZ9ALtmAKVaAJmXANZaAJlXAJZdAJxaAJlZAJdbAJlbAJmQAM+UANKZANhhAJ+EAL+BAL9oAKZnAKVjAKF1ALNBd8J1AAAAKHRSTlMAa1hWXyteBTQJIEwRgUh2JjJon21wcBgNfmc+JlOBQjwezWF2l5dXzkW3/wAAAHpJREFUeNokhQOCA1EAxTL85hi7dXv/E5YPCYBq5DeN4pcqV1XbtW/xTVMIMAZE0cBHEaZhBmIQwCFofeprPUHqjmD/+7peztd62dWQRkvrQayXkn01f/gWp2CrxfjY7rcZ5V7DEMDQgmEozFpZqLUYDsNwOqbnMLwPAJEwCopZxKttAAAAAElFTkSuQmCC

[gh-actions]: https://github.com/snowplow-incubator/snowplow-react-native-tracker/actions
[gh-actions-image]: https://github.com/snowplow-incubator/snowplow-react-native-tracker/workflows/build/badge.svg?branch=master

[license]: https://www.apache.org/licenses/LICENSE-2.0
[license-image]: https://img.shields.io/badge/license-Apache--2-blue.svg?style=flat

[releases]: https://www.npmjs.com/package/@snowplow/react-native-tracker
[release-image]: https://img.shields.io/npm/v/@snowplow/react-native-tracker

[react-native-v]: https://www.npmjs.com/package/@snowplow/react-native-tracker
[react-native-v-image]: https://img.shields.io/npm/dependency-version/@snowplow/react-native-tracker/peer/react-native

[downloads-dm]: https://www.npmjs.com/package/@snowplow/react-native-tracker
[downloads-dm-image]: https://img.shields.io/npm/dm/@snowplow/react-native-tracker

[techdocs]: https://docs.snowplowanalytics.com/docs/collecting-data/collecting-from-own-applications/react-native-tracker/
[techdocs-image]: https://d3i6fms1cm1j0i.cloudfront.net/github/images/techdocs.png

[react-native]: https://reactnative.dev/
[react-native-environment]: https://reactnative.dev/docs/environment-setup
[snowplow-micro]: https://github.com/snowplow-incubator/snowplow-micro
[snowplow-mini]: https://github.com/snowplow/snowplow-mini
