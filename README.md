# Repro for issue 6841

# Versions

platform: macOS 14.3.1<br>
firebase-tools: v13.4.1<br>
python: v3.12<br>
google-cloud-datastore: v2.19.0<br>
cloud-firestore-emulator: v1.19.2<br>
Google Cloud SDK: 467.0.0

## Steps to reproduce

1. Run `python3.12 -m venv venv`
1. Run `. venv/bin/activate` to activate the venv
1. Run `python3.12 -m pip install -r requirements.txt` to install dependencies
1. On a separate terminal, satrt the emulator by running `gcloud emulators firestore start --host-port=127.0.0.1:10901 --database-mode=datastore-mode`
1. Run `python3.12 main.py`

## Error on emulator

```
[firestore] Mar 08, 2024 8:26:21 PM com.google.cloud.datastore.emulator.firestore.websocket.WebSocketServer start
[firestore] INFO: Started WebSocket server on ws://127.0.0.1:55259
[firestore] API endpoint: http://127.0.0.1:10901
[firestore] If you are using a library that supports the FIRESTORE_EMULATOR_HOST environment variable, run:
[firestore]
[firestore]    export FIRESTORE_EMULATOR_HOST=127.0.0.1:10901
[firestore]
[firestore] If you are running a Firestore in Datastore Mode project, run:
[firestore]
[firestore]    export DATASTORE_EMULATOR_HOST=127.0.0.1:10901
[firestore]
[firestore] Note: Support for Datastore Mode is in preview. If you encounter any bugs please file at https://github.com/firebase/firebase-tools/issues.
[firestore] Dev App Server is now running.
[firestore]
[firestore] Mar 08, 2024 8:26:39 PM io.gapi.emulators.netty.HttpVersionRoutingHandler channelRead
[firestore] INFO: Detected HTTP/2 connection.
[firestore] Mar 08, 2024 8:26:39 PM com.google.cloud.datastore.emulator.impl.util.WrappedStreamObserver onError
[firestore] WARNING: Operation failed: null
[firestore] java.util.NoSuchElementException
[firestore]     at com.google.common.collect.RegularImmutableSortedSet.last(RegularImmutableSortedSet.java:222)
[firestore]     at com.google.cloud.datastore.emulator.impl.firestore.FirestoreEmulatorHelper.reserveIds(FirestoreEmulatorHelper.java:420)
[firestore]     at com.google.cloud.datastore.emulator.impl.firestore.CloudDatastoreV1.reserveIds(CloudDatastoreV1.java:492)
[firestore]     at com.google.cloud.datastore.emulator.impl.firestore.CloudDatastoreV1Router.reserveIds(CloudDatastoreV1Router.java:108)
[firestore]     at com.google.cloud.datastore.emulator.firestore.cloudv1.CloudDatastoreV1GrpcAdapter$1.lambda$reserveIds$6(CloudDatastoreV1GrpcAdapter.java:98)
[firestore]     at com.google.cloud.datastore.emulator.firestore.cloudv1.CloudDatastoreV1GrpcAdapter.unary(CloudDatastoreV1GrpcAdapter.java:49)
[firestore]     at com.google.cloud.datastore.emulator.firestore.cloudv1.CloudDatastoreV1GrpcAdapter$1.reserveIds(CloudDatastoreV1GrpcAdapter.java:98)
[firestore]     at com.google.datastore.v1.DatastoreGrpc$MethodHandlers.invoke(DatastoreGrpc.java:766)
[firestore]     at io.grpc.stub.ServerCalls$UnaryServerCallHandler$UnaryServerCallListener.onHalfClose(ServerCalls.java:182)
[firestore]     at io.grpc.internal.ServerCallImpl$ServerStreamListenerImpl.halfClosed(ServerCallImpl.java:351)
[firestore]     at io.grpc.internal.ServerImpl$JumpToApplicationThreadServerStreamListener$1HalfClosed.runInContext(ServerImpl.java:861)
[firestore]     at io.grpc.internal.ContextRunnable.run(ContextRunnable.java:37)
[firestore]     at io.grpc.internal.SerializingExecutor.run(SerializingExecutor.java:133)
[firestore]     at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1130)
[firestore]     at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:630)
[firestore]     at java.base/java.lang.Thread.run(Thread.java:831)
[firestore]
```

## Error on main.py

```log
Traceback (most recent call last):
  File "/Users/<path>/main.py", line 25, in <module>
    main()
  File "/Users/<path>/main.py", line 21, in main
    client.reserve_ids_sequential(key, 1)
  File "/Users/<path>/venv/lib/python3.12/site-packages/google/cloud/datastore/client.py", line 1010, in reserve_ids_sequential
    self._datastore_api.reserve_ids(
  File "/Users/<path>/venv/lib/python3.12/site-packages/google/cloud/datastore_v1/services/datastore/client.py", line 1354, in reserve_ids
    response = rpc(
               ^^^^
  File "/Users/<path>/venv/lib/python3.12/site-packages/google/api_core/gapic_v1/method.py", line 131, in __call__
    return wrapped_func(*args, **kwargs)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/Users/<path>/venv/lib/python3.12/site-packages/google/api_core/retry/retry_unary.py", line 293, in retry_wrapped_func
    return retry_target(
           ^^^^^^^^^^^^^
  File "/Users/<path>/venv/lib/python3.12/site-packages/google/api_core/retry/retry_unary.py", line 153, in retry_target
    _retry_error_helper(
  File "/Users/<path>/venv/lib/python3.12/site-packages/google/api_core/retry/retry_base.py", line 212, in _retry_error_helper
    raise final_exc from source_exc
  File "/Users/<path>/venv/lib/python3.12/site-packages/google/api_core/retry/retry_unary.py", line 144, in retry_target
    result = target()
             ^^^^^^^^
  File "/Users/<path>/venv/lib/python3.12/site-packages/google/api_core/timeout.py", line 120, in func_with_timeout
    return func(*args, **kwargs)
           ^^^^^^^^^^^^^^^^^^^^^
  File "/Users/<path>/venv/lib/python3.12/site-packages/google/api_core/grpc_helpers.py", line 78, in error_remapped_callable
    raise exceptions.from_grpc_error(exc) from exc
google.api_core.exceptions.Unknown: None
```
