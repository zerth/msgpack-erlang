# MessagePack Erlang

![Travis](https://secure.travis-ci.org/msgpack/msgpack-erlang.png)

![Drone.io](https://drone.io/github.com/msgpack/msgpack-erlang/status.png)

## prequisites for runtime

[Erlang/OTP](http://erlang.org/), >= R15B -- for older version, rebar won't work.
Also based on [the new msgpack spec 232a0d](https://github.com/msgpack/msgpack/blob/232a0d14c6057000cc4a478f0dfbb5942ac54e9e/spec.md).

## edit rebar.config to use in your application

```erlang
{deps, [
  {msgpack, ".*",
    {git, "git://github.com/msgpack/msgpack-erlang.git", "master"}}
]}.
```

## Simple deserialization

```erlang
Ham = msgpack:pack(Spam),
{ok, Spam} = msgpack:unpack(Ham).
```

## Stream deserialization

```erlang
{Term0, Rest0} = msgpack:unpack_stream(Binary),
{Term1, Rest1} = msgpack:unpack_stream(Rest0),
...
```

## String type

Now this supports string type!

```erlang
Opt = [{enable_str, true}]
{ok, "埼玉"} = msgpack:unpack(msgpack:pack("埼玉", Opt), Opt).
 => {ok,[22524,29577]}
```

There are several options for `msgpack:pack/2` and `msgpack:unpack/2` .
See `msgpack:options()` in `msgpack.hrl`.

## Ext type

Now msgpack-erlang supports ext type. Now you can serialize everything
with your original (de)serializer. That will enable us to handle
erlang- native types like `pid()`, `ref()` contained in `tuple()`. See
`test/msgpack_ext_example_tests.erl` for example code.

```erlang
Packer = fun({ref, Ref}, Opt) when is_ref(Ref) -> {ok, {12, term_to_binary(Ref)}} end,
Unpacker = fun(12, Bin) -> {ok, {ref, binary_to_term(Ref)}},
Ref = make_ref(),
Opt = [{ext,{Packer,Unpacker}}],
{ok, {ref, Ref}} = msgpack:unpack(msgpack:pack({ref, Ref}, Opt), Opt).
```

This is still experimental feature, so I'm waiting for your feedback.

## Compatibility mode

To use as same with [old spec](https://github.com/msgpack/msgpack/blob/master/spec-old.md):

```erlang
OldHam = msgpack:pack(Spam, [{enable_str,false}]),
{ok, Spam} = msgpack:unpack(OldHam, [{enable_str,false}]).
```

Since 0.2.3 now it's **false by default**.

## Further tests

See [msgpack-erlang-tests](http://github.com/kuenishi/msgpack-erlang-tests) for further tests

## License

Apache License 2.0