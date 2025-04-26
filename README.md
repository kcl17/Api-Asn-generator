# Api-Asn-Generator

## OpenAPI and ASN.1 Code Generation for 5GC APIs

This project provides complete instructions for generating **Go client libraries** from **5G Core Network Function OpenAPI specifications** and **C source code** from **ASN.1 NGAP definitions**. It supports multiple 3GPP releases (e.g., Release 15, 16, 17, 18) using the [jdegre/5GC_APIs](https://github.com/jdegre/5GC_APIs) repository.

---

## 📋 Prerequisites

Before running any of the commands, ensure you have the following installed:

- **Node.js** (v14 or later)  
  Install from [https://nodejs.org/](https://nodejs.org/)

Or, if you don't want npm setup you can use docker environment for generating APIs

- **Docker Setup**
  Install docker from [docker](https://docs.docker.com/engine/install)

- **npm** (comes bundled with Node.js)

- **Go** (v1.18 or later)  
  Install from [https://go.dev/doc/install](https://go.dev/doc/install)

- **asn1c** (ASN.1 to C compiler)  
  Install via package manager:
  - Ubuntu: `sudo apt-get install asn1c`
  - macOS (Homebrew): `brew install asn1c`
  - Or build from source: [http://lionet.info/asn1c/](http://lionet.info/asn1c/)

---

## 📂 Source Repositories

- **5GC OpenAPI YAML files**: [jdegre/5GC_APIs](https://github.com/jdegre/5GC_APIs)  
  Contains OpenAPI specifications (`*.yaml`) for all 5G Core Network Functions (AMF, SMF, UDM, UDR, PCF, NRF, AUSF, etc.).  
  Supports multiple 3GPP releases: 15, 16, 17, 18.

- **NGAP ASN.1 Definitions**:  
  Typically located inside the `asn1/` directory of your project.  
  Example: `asn1/Ngap_38413.asn` (aligned with 3GPP 38.413 specification).

---

## ⚙️ OpenAPI Go Client Generation

You can generate Go client libraries from OpenAPI YAML specifications using the OpenAPI Generator CLI.

### 1. Install OpenAPI Generator CLI globally

```bash
npm install @openapitools/openapi-generator-cli -g
```

### 2. Generate Go client code

```bash
openapi-generator-cli generate -i path/to/your_openapi.yaml -g go -o output_dir
```

### 3. Docker Method
Move to the directory where your_openapi.yaml is placed.
```bash
docker run --rm \
  -v "${PWD}:/local" openapitools/openapi-generator-cli generate \
  -i /local/<your_openapi.yaml> \
  -g go \
  -o /local/out/go \
  --skip-validate-spec \
  --package-name <package name>
```
- `-i`: Input OpenAPI YAML file (e.g., from the 5GC_APIs repo).
- `-g`: Target language (`go` for Golang client).
- `-o`: Output directory where the generated code will be placed.

✅ **Tip**:  
You can generate libraries for any NF (AMF, SMF, etc.) and any release (R15, R16, R17, R18) by simply changing the input YAML file.

---

## ⚙️ ASN.1 NGAP Code Generation

You can generate C code from ASN.1 definitions (e.g., NGAP protocol used between gNB and AMF) using `asn1c`.

> Note: if you are using wireshark's asn files for source then you should bundle all files in a single file so that asn1c works properly

### 1. Generate NGAP C Source Code

```bash
asn1c -fcompound-names -pdu=auto asn1/Ngap_38413.asn
```

- `-fcompound-names`: Generates C structures with compound names to avoid name clashes.
- `-pdu=auto`: Automatically generate encoding/decoding support for PDUs (Protocol Data Units).
- `asn1/Ngap_38413.asn`: Path to the NGAP ASN.1 specification file.

The output will be a set of `.c` and `.h` files representing NGAP messages and types.

---

## 📌 Notes

- When using the OpenAPI generator, you can customize the generated code further by supplying additional `-p` parameters (like package names, enum naming, etc.).
- Review the generated Go clients carefully — sometimes manual tweaks are necessary due to inconsistencies in OpenAPI specifications.
- The `asn1c` tool may require patching or tweaking `.asn` files if they are not fully aligned with the compiler's expectations.

---

## 📚 References

- [OpenAPI Generator CLI Documentation](https://openapi-generator.tech/docs/installation)
- [asn1c Compiler Documentation](http://lionet.info/asn1c/)
- [jdegre/5GC_APIs GitHub Repository](https://github.com/jdegre/5GC_APIs)