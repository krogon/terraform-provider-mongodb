# Terraform Provider Mongodb
![GitHub go.mod Go version](https://img.shields.io/github/go-mod/go-version/FelGel/terraform-provider-mongodb?logo=go&style=flat-square)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/FelGel/terraform-provider-mongodb?logo=git&style=flat-square)
![GitHub](https://img.shields.io/github/license/FelGel/terraform-provider-mongodb?color=yellow&style=flat-square)
![GitHub Workflow Status](https://img.shields.io/github/workflow/status/FelGel/terraform-provider-mongodb/golangci?logo=github&style=flat-square)
![GitHub issues](https://img.shields.io/github/issues/FelGel/terraform-provider-mongodb?logo=github&style=flat-square)

This repository is a Terraform Mongodb for [Terraform](https://www.terraform.io).

### Requirements

- [Terraform](https://www.terraform.io/downloads.html) >= 0.13
- [Go](https://golang.org/doc/install) >= 1.25

### Publishing (`registry.terraform.io/krogon/mongodb`)

This fork publishes as **`krogon/mongodb`**, not `FelGel/mongodb`. Repo name must stay `terraform-provider-mongodb`. Tags must match `v*` (first cut of this namespace: `v3.2.0`).

**Terraform Registry (once per provider)**

1. GitHub user/org `krogon` must own the `krogon` namespace on [registry.terraform.io](https://registry.terraform.io).
2. Register the **public** half of the signing GPG key on that namespace ([Publishing providers](https://developer.hashicorp.com/terraform/registry/providers/publishing)). One GPG key per namespace — use the same key as other `krogon` providers (e.g. `terraform-provider-postgresql`). Signing with a different key than HashiCorp has on file makes the Registry reject the version.
3. Create provider `mongodb` linked to repo `krogon/terraform-provider-mongodb`. Registry installs a GitHub webhook; it ingests a `v*` GitHub Release that includes a signed checksum and `terraform-registry-manifest.json`.

**GitHub Actions secrets** (Settings → Secrets and variables → Actions on **this** repo, not in code). Copy `GPG_PRIVATE_KEY` / `PASSPHRASE` from `terraform-provider-postgresql` if that namespace already publishes.

| Secret | Used by | What to put |
| --- | --- | --- |
| `GPG_PRIVATE_KEY` | `crazy-max/ghaction-import-gpg@v7` | ASCII-armored **private** key (`gpg --armor --export-secret-keys <KEY_ID>`). Must be the same key whose **public** half is registered on Terraform Registry. |
| `PASSPHRASE` | same action | Passphrase for that private key. Empty-string secret if the key has no passphrase (prefer a passphrase). |
| `GITHUB_TOKEN` | goreleaser (create GitHub Release + upload assets) | Do **not** create this. Default Actions token. This workflow sets `permissions: contents: write` so the token can publish the release. |

`GPG_FINGERPRINT` is **not** a secret. The import-gpg step outputs it; goreleaser uses it to `--detach-sign` `SHA256SUMS`.

**Sanity check before the first tag:** Actions secrets present, Registry provider + GPG key saved, `providerAddr` in `main.go` is `registry.terraform.io/krogon/mongodb`. Then `git tag v3.2.0 && git push --tags`. A missing secret fails at import-gpg; a GPG/Registry mismatch publishes a GitHub Release that Registry silently (or with an email) refuses.

### Installation

1. Clone the repository
1. Enter the repository directory
1. Build the provider using the `make install` command:

````bash
git clone https://github.com/FelGel/terraform-provider-mongodb
cd terraform-provider-mongodb
make install
````

### To test locally 

**1.1: create mongo image  with ssl**


````bash
cd docker/docker-mongo-ssl
docker build -t mongo-local .
````
**1.2: create ssl for localhost**


*follow the instruction in this link*

https://ritesh-yadav.github.io/tech/getting-valid-ssl-certificate-for-localhost-from-letsencrypt/


````bash
nano /etc/hosts
127.0.0.1   kaginar.herokuapp.com   ### add this line 
````


**1.3: start the docker-compose**
````bash
cd docker
docker-compose up -d
````
**1.4 : create admin user in mongo**

````bash
$ docker exec -it mongo -c mongo
> use admin
> db.createUser({ user: "root" , pwd: "root", roles: ["userAdminAnyDatabase", "dbAdminAnyDatabase", "readWriteAnyDatabase"]})
````
**2: Build the provider**

follow the [Installation](#Installation)

**3: Use the provider**

````bash
cd mongodb
make apply
````
