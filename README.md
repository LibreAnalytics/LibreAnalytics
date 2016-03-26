# LibreAnalytics

Free, simple, privacy-conscious analytics for apps.

**Status:** Initial development. You're welcome to join us.

Privacy-conscious:

* Does not track users.
* Does not store every data point under the sun.
* Install it on your own server, or even run locally on your computer.
* Opt-in or opt-out.
* Show your users _exactly_ what is being sent, and to whom.

Simple:

* For the rest of us.
* No Big Data™.
* The server is very simple, client apps do most of the work.
* Focuses on providing answers to specific questions, not just collecting data.
* Easy to install (a single binary with no dependencies).

Distributed under the terms of the permissive [MIT license](LICENSE).


## Goals

### Version 1.0

Daily or weekly (or N-day sliding window) metrics:

* Active Users — the number of users who performed at least one action in your app
* New Users — number of new installations

Plus active users breakdown by:

* app version
* OS version
* device model

Additional metrics:

* User Rentention (based on reported user age)
* Sessions: Count, Duration

Other concerns:

* Focus on tracking the behavior of active users. If someone “bounced” (installed, tried it for a few minutes and deleted), we don't necessarily have to count them at all.
* Only display the most relevant data.
* Only _send_ the most relevant data.
* Only start sending data / ask for opt-in after a specific threshold has been reached (X days of active usage, Y calendar days since installation, Z actions taken)
* Use medians instead of averages where possible.
* Tests for everything!


### Version 1.1 or Later

* Segmentation
* Heavy/moderate/light usage segments in particular
* User satisfaction surveys (like/dislike, send feedback)
* Ask for app ratings
* Crash reporting
* Crash-free users/session counts
* Serverless operation (based solely on web server logs — Apache, Amazon S3, etc)


## Data

The overall idea is to keep the server as simple and as uninformed as possible:

1. The apps ping the server once per reporting interval (once a day, once a week), and each ping increases the number of “votes” for a specific event or value. The server simply counts the pings without storing the raw data.

2. To avoid the possibility of fingerprinting, apps should avoid sending raw numberic data, instead grouping the numbers into a few broad categories (e.g. instead of sending the number of actions performed, we should report “heavy”, “moderate” or “light” usage of a specific feature, or use ranges like “1–9”, “10–29”, “30–99”, “100–300”, etc).

3. Similarly, we should consider only reporting notable data, e.g. only reporting moderate/heavy usage and ignoring light usage.

4. We may want to also consider sending different bits of data separately, so that it would be impossible to fingerprint users based on their entire data set.

5. Segmentation is performed by clients self-reporting the segments they belong to, and the votes are additionally counted per segment. Segment definitions _may_ come from the server dynamically, so that you wouldn't have to resubmit the app just to update the segments. (Segmentation is a post-1.0 goal anyway.)

6. We should use standardized event names, and custom events should be annotated with additional metadata (e.g. “roles”) that allow to present them in a sane and meaningful way.

7. Raw data should be stored for a very limited time (e.g. last 7 or 30 days only) for the sole purpose of abuse prevention, so that the votes that came from a specific IP could be rolled back. The storage needs to be designed to be able to decrement votes (e.g. it cannot be a bloom filter).

8. It may make sense to send and store the raw data in an encrypted form using assymetric crpytography, so that only the app owner can decrypt it. Which would also means that it makes sense to separate data collection (that would store the incoming data without decrypting it) from data processing (that needs to decrypt the data, but may optionally run on a local computer instead of the server).


## Spec to be continued...
