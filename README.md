# OTP

A simple One Time Password (OTP) library in C/C++

This library is fork of `patzol768/cpp-otp`. See [here](https://github.com/patzol768/cpp-otp)

It remained compatible with Authy and Google Authenticator. (Be aware, that the latter one [currently ignores some parameters](https://github.com/google/google-authenticator/wiki/Key-Uri-Format).)


## External Libraries Needed

* OpenSSL is recommended to use with this library. You can easily switch off in the Makefile, but in case you do, you have to provide HMAC algorithms from another source. (Check the `register_hmac_algo()` method.)
* The QR code generator library is included in source format.

_____________

## License

This library is licensed under MIT License.

This product optionally includes software developed by the OpenSSL Project for use in the [OpenSSL Toolkit](http://www.openssl.org/).


## Usage

Create a TOTP, have a fresh code and check the remaining time to the next code:

```cpp
	size_t interval = 30;
	size_t digits = 6;
	std::string base32_secret = "THISISASECRET345";

	auto totp = cotp::TOTP(base32_secret, "SHA1", digits, interval);

	auto current_code = totp.code();

	auto remaining_secs = totp.seconds_to_next_code();
```

Create a pseudo-random secret (random number generator seeded during library init, with a very basic seeding):

```cpp
	size_t base32_len = 16;

	auto base32_new_secret = cotp::OTP::random_base32(base32_len);
```

Get a current code based on an existing URI:

```cpp
	auto otp = cotp::OTP_factory::get_instance().create("otpauth://totp/account%40example.com:name1?secret=THISISASECRET345&issuer=account%40example.com&algorithm=SHA1&digits=6&period=30");
	auto current_code = otp->code();
```

Verify a code for the current time with a validity window:

```cpp
	// have totp defined

	uint64_t received_code = 123456;
	size_t validity_window = 4;

	auto result = totp.verify(received_code, validity_window);
```

Create a QR code for the URI in svg format:

```cpp
	// have totp defined

	cotp::QR_code totp_qr;
	totp_qr.set_content(totp);
	auto totp_svg = totp_qr.get_svg();
```

<img src="/assets/totp.svg" width="200" height="200"/>

Add some scaled decorations to the QR code:

```cpp
	// have hotp defined

	cotp::QR_decoration decoration = { R"DECO(
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg xmlns="http://www.w3.org/2000/svg" version="1.1" viewBox="%viewbox%" stroke="none">
	<g transform="scale(%scale%)">
		<style>.caption { font: bold 190px sans-serif; fill: white; }</style>
		<rect x="0" y="0" width="1144" height="1332" rx="50" fill="#444444"/>
		<text x="90" y="204" class="caption">SCAN ME</text>
		<rect x="72" y="264" width="1000" height="1000" fill="#FFFFFF"/>
	</g>
	<g transform="translate(%translate%)">
		%qr_code%
	</g>
</svg>)DECO",
	1144,
	1332,
	72,
	264
};

	cotp::QR_code hotp_qr;
	hotp_qr.set_content(hotp);
	hotp_qr.set_decoration(decoration);
	auto hotp_svg = hotp_qr.get_svg();
```
<img src="/assets/hotp.svg" width="229" height="264"/>

Check out the test file for more.

Notes:
* According to several papers out there, the SHA-1 with 6 digits is the more widely used / supported version.
* Watch on the potential exceptions. See details in code.
* The OTP/TOTP/HOTP class implementations use `assert()` currently. May change later on.

## Building

Use `cmake -DCMAKE_BUILD_TYPE=Release . and make`.

The libraries are created in `.so` format and not added to the system libraries.

## TODO

- [ ] possibly add option to read QR
