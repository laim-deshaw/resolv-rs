# Resolv

DNS resolution via glibc.

Note: If you are only looking up IP addresses for DNS names and don't need other records
(MX, TXT, etc), consider other more portable rust libraries like `dns-lookup`.

This uses `libresolv.so` which is typically configured via `/etc/resolv.conf` to do DNS
resolution.  It allows you to look up DNS resource records of any type (e.g. A, AAAA, MX, TXT,
etc), use recursion (if your system's DNS resolver permits it), and perform the DNS search
algorithm to complete incomplete names and use your `/etc/hosts` file.

## Example

````rust
extern crate resolv;

use resolv::{Resolver, Class, RecordType};
use resolv::record::MX;

fn main() {
    // You must create a mutable resolver object to hold the context.
    let mut resolver = Resolver::new().unwrap();

    // .query() and .search() are the main interfaces to the resolver.
    let mut response = resolver.query(b"gmail.com", Class::IN,
                                      RecordType::MX).unwrap();

    // You can iterate through answers as follows.  You must specify the
    // type of record.  A run-time error will occur if the records
    // decoded are of the wrong type.
    for answer in response.answers::<MX>() {
        println!("{:?}", answer);
    }
}
````

## Building

You need to have bindgen version at least 0.62 installed, for example:

```
cargo install bindgen-cli
```

## Limitations

You cannot specify a DNS server separate from editing `/etc/resolv.conf` for the entire
system.

Not all NS record types are supported yet.

The thread-safe interface is used, which may not be available on older systems.

Depending on the version of glibc installed on the system, one of several
adapter modules is chosen. It is likely that some systems will require making a
new adapter module from one of the existing ones. These are in
libresolv-sys/lib.d. Pull requests with additional adapters are welcome.

## License

Licensed under either of

 * Apache License, Version 2.0, ([LICENSE-APACHE](LICENSE-APACHE) or http://www.apache.org/licenses/LICENSE-2.0)
 * MIT license ([LICENSE-MIT](LICENSE-MIT) or http://opensource.org/licenses/MIT)

at your option.

### Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in the work by you, as defined in the Apache-2.0 license, shall
be dual licensed as above, without any additional terms or conditions.
