
Data must be serialized so it can be sent over the wire, such has in [HTTP](HTTP.md) requests or RCP calls.


### [HTML](../Software%20Engineering/HTML.md)
The declarative markup language of the internet. You can add in content and add dynamic rendering with JavaScript. Using [CSS](../Software%20Engineering/CSS.md), you can add styling to the HTML. Browsers render HTML for the user


### XML
The original data format to be sent over the web. It is old and bad. The format is similar to HTML with opening and closing tags for all the data


### JSON
The most popular and important format for sending data, usually over [REST APIs](REST%20API.md). it is a simple format, that looks like [Hash Maps](../Data%20Structures%20&%20Algorithms/Data%20Structures/Hash%20Maps.md). JSON accepts the following data types:

- String
- Number
- Array
- Boolean
- Object (a JSON object)
- null

JSON is a great and popular format, but it is a text-based format and thus has a severe penalty in the serializing and de-serializing process, and it has a much larger data footprint. These issues led to the development of multiple binary serialized data formats.


### BSON
Binary JSON. This format looks almost identical to JSON, but it is a binary format and thus has a smaller footprint and faster encoding/decoding. BSON is used by [MongoDB](../Databases/Document%20Databases.md) for its database storage, as it also adds support for various other types like Date and Binary, that aren't supported in JSON.


### Protocol Buffer
Google's encoding format that is used in gRPC. It is meant for very fast processing by programming languages. The schema of the message must be known beforehand. It guarantees type safety, backwards compatibility, and a smaller footprint.


### Base64
Base64 is an encoding algorithm to convert [Binary](../Electrical%20Engineering/Digital/Binary.md) data into an ASCII string which can be sent as a regular string over the network. This is not necessarily a serialization format, but is used in combination with one of the above formats to safely deliver binary data over the web.