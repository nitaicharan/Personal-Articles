# Authenticating service-to-service

Created: April 24, 2022 9:16 PM
Finished: No
Source: https://cloud.google.com/run/docs/authenticating/service-to-service

If your architecture is using multiple services, these services likely need to communicate with each other, using either asynchronous or synchronous means. Many of these services may be private and require credentials for access.

**Note:** There are no networking charges for service to service traffic between two services in the same region.

For *asynchronous* communication, you can use the following Google Cloud services:

- [Cloud Tasks](https://cloud.google.com/run/docs/triggering/using-tasks) for one to one asynchronous communication
- [Pub/Sub](https://cloud.google.com/run/docs/events/pubsub-push) for one to many, one to one, and many to one asynchronous communication
- [Cloud Scheduler](https://cloud.google.com/run/docs/triggering/using-scheduler) for regularly scheduled asynchronous communication
- [Eventarc](https://cloud.google.com/eventarc/docs/creating-triggers) for event-based communication

In all of these cases, the service used manages the interaction with the receiving service, based on the configuration you set up.

But for *synchronous* communication, your service calls another service directly, over HTTP, using its endpoint URL. For this use case, you should make sure that each service is only able to make requests to specific services. For example, if you have a `login` service, it should be able to access the `user-profiles` service, but not the `search` service.

In this situation, Google recommends that you use [IAM](https://cloud.google.com/iam/docs/understanding-service-accounts) and a service identity based on a [per-service user-managed service account](https://cloud.google.com/run/docs/securing/service-identity#per-service-identity) that has been granted the [minimum set](https://cloud.google.com/iam/docs/understanding-service-accounts#granting_minimum) of permissions required to do its work.

In addition, the request must present proof of the calling service's identity. To do this, configure your calling service to add a Google-signed [OpenID Connect](https://openid.net/connect/) ID token as part of the request.

### Set up the service account

**Note:** If you do not have a service account you want to use, you can [create a new one](https://cloud.google.com/iam/docs/creating-managing-service-accounts#creating).

To set up a service account, you configure the receiving service to accept requests from the calling service by making the calling service's service account a **member** on the receiving service. Then you grant that service account the Cloud Run Invoker (`roles/run.invoker`) role. To do both of these tasks, follow the instuctions in the appropriate tab:

1. Select the receiving service.
2. Click **Show Info Panel** in the top right corner to show the **Permissions** tab.
3. In the **Add members** field, enter the identity of the calling service. This is usually an email address, by default `PROJECT_NUMBER-compute@developer.gserviceaccount.com`.
4. Select the `Cloud Run Invoker` role from the **Select a role** drop-down menu.
5. Click **Add**.

### Acquire and configure the ID token

Once the calling service account has been granted the proper role, you need to:

1. Fetch a Google-signed ID token with the audience claim (`aud`) set to the URL of the receiving service.
    
    **Note:** Custom domains are currently not supported for the `aud` value.
    
2. Include the ID token in an `Authorization: Bearer ID_TOKEN` header in the request to the receiving service.

The easiest and most reliable way to manage this process is to use the authentication libraries, as shown below, to generate and use this token. This code works in **any environment** - even outside of Google Cloud - where the libraries can obtain authentication credentials, including environments that support local [Application Default Credentials](https://cloud.google.com/docs/authentication/production#automatically).

```
/**
 * TODO(developer): Uncomment these variables before running the sample.
 */// Example: https://my-cloud-run-service.run.app/books/delete/12345// const url = 'https://TARGET_HOSTNAME/TARGET_URL';// Example (Cloud Run): https://my-cloud-run-service.run.app/// const targetAudience = 'https://TARGET_AUDIENCE/';const {GoogleAuth} = require('google-auth-library');const auth = new GoogleAuth();

async function request() {
  console.info(`request ${url} with target audience ${targetAudience}`);
  const client = await auth.getIdTokenClient(targetAudience);
  const res = await client.request({url});
  console.info(res.data);}

request().catch(err => {
  console.error(err.message);
  process.exitCode = 1;});
```

### Use the metadata server

If for some reason you **cannot** use the authentication libraries, you can fetch an ID token from the [Compute metadata server](https://cloud.google.com/run/docs/securing/service-identity#identity_tokens) while your container is running on Cloud Run. Note that this method does not work outside of Google Cloud, including from your local machine.

```
curl "http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/identity?audience=[AUDIENCE]" \
     -H "Metadata-Flavor: Google"
```

Where AUDIENCE is the URL of the service you are invoking.

**Note:** ID tokens are [JSON Web Tokens (JWTs)](https://en.wikipedia.org/wiki/JSON_Web_Token) that expire approximately an hour after creation. If you fetch tokens from the [metadata server](https://cloud.google.com/run/docs/securing/service-identity#identity_tokens), you will always get a valid token. If you choose to cache tokens yourself, you can decode the token and check the `exp` time to see if you need to refresh the token.

For an end-to-end walkthrough of an application using this service-to-service authentication technique, follow the [securing Cloud Run services tutorial](https://cloud.google.com/run/docs/tutorials/secure-services).

## Calling from outside GCP

The best way to call a private service from outside Google Cloud is to use the authentication libraries as [described above](https://cloud.google.com/run/docs/authenticating/service-to-service#acquire-token), having set up [Application Default Credentials](https://cloud.google.com/docs/authentication/production#automatically).

You can acquire a Google-signed ID token using a self-signed JWT, but this is quite complicated and potentially error-prone. The basic steps are:

1. Self-sign a service account JWT with the `target_audience` claim set to the URL of the receiving service.
2. Exchange the self-signed JWT for a Google-signed ID token, which should have the `aud` claim set to the above URL.
3. Include the ID token in an `Authorization: Bearer ID_TOKEN` header in the request to the service.

You can examine this [Cloud Functions example](https://cloud.google.com/functions/docs/securing/authenticating#exchanging_a_self-signed_jwt_for_a_google-signed_id_token) for a sample of the above steps.