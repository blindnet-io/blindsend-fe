# Blindsend web UI

This project is a web client for [blindsend](https://github.com/blindnet-io/blindsend), an open source tool for private, end-to-end encrypted file exchange between two parties.

Blindsend web client provides a web UI that uses blindsend [server (API)](https://github.com/blindnet-io/blindsend-be) for private file exchange. It also performs local encryption and decryption of the exchanged files, so that all files exchanged via blindsend are always encrypted and decrypted only on Sender's/Receiver's local machines. Web UI has been tested on Chrome, Chromium, and Firefox (all desktop).

Blindsend web UI is intended to be used together with [blindsend server](https://github.com/blindnet-io/blindsend-be). A demo is avalable [here](https://blindsend.xyz).

## Installation instructions

Before building blindsend web UI, you need to have [npm](https://www.npmjs.com/get-npm) installed.

To build blindsend web UI, run the following command in the project root
```bash
npm run build
```

If you are using a server instance without the https connection, build the web UI by running the command below. In this case however, you should never use that instance for purposes other than testing. 
```bash
npm run build-local
```

This will create a `dist` folder in project's root. To integrate blindsend web UI with the server, [install blindsend server](https://github.com/blindnet-io/blindsend-be) and put the `dist` folder in the same location as your `blindsend.jar` before running the server.

## Design

When uploading a file, the file is split into chunks of size `uploadChunkSize` (default 4MB) found in `globals.ts` file. So byte arrays of 4MB are loaded into the memory, encrypted and sent to the server.  
When js `fetch` api implementations start supporting `ReadableStream<Uint8Array>` request bodies, one request will be enough to transfer the file.

When downloading a file, the entire file is downloaded in a single request. Body of a response of the `fetch` api has type `ReadableStream<Uint8Array>` so the file bytes can be decrypted (transformed with `TransformStream`) before the entire file arrives. As soon as a tiny part (`encryptionChunkSize` in `globals.ts`) of file is decrypted, it is streamed to disk with a help from [StreamSaver](https://github.com/jimmywarting/StreamSaver.js) library. At no point is the whole file kept in memory, just the part currently being decrypted.

## Dependencies

Web UI implementation is written in [TypeScript](https://www.typescriptlang.org/) and [React](https://reactjs.org/).  
App architecture is [Elm](https://guide.elm-lang.org/architecture/) like with [elm-ts](https://github.com/gcanti/elm-ts) library as the backbone.  
For cryptography, [JavaScript + WebAssembly libsodium port](https://github.com/jedisct1/libsodium.js/) is used.  

## Current status
This project has been started by [blindnet.io](https://blindnet.io/) and is currently under development.  