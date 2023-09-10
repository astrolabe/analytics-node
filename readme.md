# Astrolabe Node SDK

## Installation

To install the Astrolabe SDK in your Node.js project, use the npm package manager:

```sh
npm install @astrolabe/analytics-node --save
```

Alternatively, use yarn:

```sh
yarn add @astrolabe/analytics-node
```

## Configuration

First, you'll need to import the SDK into your application:

```js
import { Analytics } from '@astrolabe/analytics-node';
```

Next, instantiate the client using the write key you've received when you created your Astrolabe account:

```js
const analytics = new Analytics('YOUR_WRITE_KEY');
```

Now you can use the analytics instance to track events, identify users, and more.

# API Reference

## new Analytics(writeKey, [options])

Initializes a new `Analytics` instance.

- `writeKey` (_String_): Your Astrolabe project's write key.
- `options` (_Object_, optional): Configuration options. Possible fields:
  - `flushAt` (_Number_): The number of messages to enqueue before flushing (default: 20).
  - `flushInterval` (_Number_): The duration (in ms) to wait before flushing the queue automatically (default: 10000 ms).
  - `enable` (_Boolean_): If set to `false`, turns off data queuing and sending (default: `true`).
  - `axiosConfig` (_Object_): Additional configuration for Axios.
  - `axiosRetryConfig` (_Object_): Additional configuration for Axios Retry.
  - `retryCount` (_Number_): Number of times to retry a failed request (default: 3).
  - `errorHandler` (_Function_): Custom error handling function.

Example:

```javascript
const analytics = new Analytics('YOUR_WRITE_KEY', {
  flushAt: 50,
  flushInterval: 30000, // flush every 30 seconds
});
```

## Tracking Events

To track events, you can use the `track` method:

```javascript
analytics.track({
  userId: 'USER_ID',
  event: 'Signed In',
  properties: {
    browser: 'chrome',
  },
});
```

Parameters:

- `userId`: (Type: `String`, Optional) The ID for this user in your database. \_Note: At least one of `userId` or `anonymousId` must be included in any track call.
- `anonymousId`: (Type: `String`, Optional) An ID associated with the user when you don’t know who they are (for example, the `anonymousId` generated by analytics.js). Note: You must include at least one of `userId` or `anonymousId` in all track calls.
- `event`: (Type: `String`) The name of the event you’re tracking. It is recommended to use human-readable names like 'Song Played' or 'Status Updated'.
- `properties`: (Type: `Object`, Optional) A dictionary of properties for the event. For instance, if the event was 'Product Added', it might have properties like price or product.
- `timestamp`: (Type: `Date`, Optional) A JavaScript date object representing when the track took place. If the track just happened, leave it out and the server’s time will be used. If you’re importing data from the past, make sure to send a timestamp.
- `context`: (Type: `Object`, Optional) A dictionary of extra context to attach to the call. Note: `context` differs from `traits` because it is not attributes of the user itself.

## Identifying Users & Setting User Traits

To identify users, you can use the `identify` method:

```javascript
analytics.identify({
  userId: 'USER_ID',
  traits: {
    email: 'test@example.com',
    // Optional
    name: 'Joe Bloggs',
    avatar: 'https://avatar.com/asd809sdhoif9as10nc29.png',
    // Add anything else about the user here
  },
});
```

Parameters:

- `userId`: (Type: `String`, Optional) The ID for this user in your database. Note: At least one of `userId` or `anonymousId` must be included in any identify call.
- `anonymousId`: (Type: `String`, Optional) An ID associated with the user when you don’t know who they are (for example, the `anonymousId` generated by analytics.js). Note: You must include at least one of `userId` or `anonymousId` in all identify calls.
- `traits`: (Type: `Object`, Optional) A dictionary of traits you know about the user. Things like: email, name, or friends.
- `timestamp`: (Type: `Date`, Optional) A JavaScript date object representing when the identify took place. If the identify just happened, leave it out as Segment uses the server’s time. If you’re importing data from the past, make sure to send a timestamp.
- `context`: (Type: `Object`, Optional) A dictionary of extra context to attach to the call. Note: `context` differs from `traits` because it is not attributes of the user itself.

## Grouping Users

To group users by organization or company, use the `group` method:

```javascript
analytics.group({
  userId: 'USER_ID',
  groupId: 'GROUP_ID',
  traits: {
    name: 'Acme Inc',
    // Optional
    avatar: 'https://avatar.com/asd809sdhoif9as10nc29.png',
    // Add anything else about the company here
  },
});
```

Parameters:

- `userId`: (Type: `String`, Optional) The ID for this user in your database. \_Note: At least one of `userId` or `anonymousId` must be included in any group call.
- `anonymousId`: (Type: `String`, Optional) An ID associated with the user when you don’t know who they are (e.g., the `anonymousId` generated by analytics.js). Note: At least one of `userId` or `anonymousId` must be included in any group call.
- `groupId`: (Type: `String`) The ID of the group.
- `traits`: (Type: `Dictionary`, Optional) A dictionary of traits you know about the group. For a company, they might be things like name, address, or phone.
- `context`: (Type: `Dictionary`, Optional) A dictionary containing any context about the request. To see the full reference of supported keys, check them out in the context reference.
- `timestamp`: (Type: `DateTime`, Optional) A DateTime object representing when the group took place. If the group just happened, leave it out and we’ll use the server’s time. If you’re importing data from the past, make sure to send a timestamp.

# Serverless environments

In serverless environemnts like AWS Lambda, your environment may finish the code execution before Astrolabe SDK is able to send events to Astrolabe API. 

You can use the `flush` method to send all queued events to Astrolabe or you can configure the SDK to flush events automatically.

## Flush events manually

```javascript
analytics.flush();
```

## Flush events automatically

If you set `flushAt` to `1`, the SDK will flush events automatically after every event.

```javascript
const analytics = new Analytics('YOUR_WRITE_KEY', {
  flushAt: 1,
});
```