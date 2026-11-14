# Lista de modelos de IA (V3RLib)

Lista **assinada** dos modelos de IA com desligamento anunciado pelos fornecedores (OpenAI, Google, Anthropic, Perplexity), com data de saida, substituto indicado, fonte oficial (URL), data da consulta e, quando conhecido, preco oficial com vigencia. Os aplicativos da V3RTECH baixam `lista.json` uma vez por dia, conferem a assinatura e so usam a lista se ela for autentica; se estiver indisponivel ou invalida, vale a ultima lista boa.

Gerada toda segunda-feira por rotina automatica do repositorio privado da biblioteca; so ha commit quando algo mudou. O historico deste repositorio e o registro de quando cada modelo foi marcado.

## Formato

`lista.json` = `{ "algoritmo": "ed25519", "lista": {...}, "assinatura": "<base64>" }`. A assinatura cobre os bytes canonicos de `lista`: JSON sem espacos, chaves de objeto ordenadas por bytes UTF-8, arrays na ordem dada. `lista.formato` e a versao do formato (atual: 1).

## Chave publica (ed25519, SPKI DER em base64)

```
MCowBQYDK2VwAyEAdf/XyHdYIM4CHZXFtKvJEv5hIHLHC31VbsiWMG+uhbY=
```

## Como verificar (Node.js)

```js
import { createPublicKey, verify } from "node:crypto";
const ordena = (v) => Array.isArray(v) ? v.map(ordena)
  : v && typeof v === "object" ? Object.fromEntries(Object.keys(v).sort((a, b) => Buffer.compare(Buffer.from(a), Buffer.from(b))).map((k) => [k, ordena(v[k])]))
  : v;
const env = await (await fetch("https://raw.githubusercontent.com/V3RTECH-DF/v3r-modelos-ia/main/lista.json")).json();
const chave = createPublicKey({ key: Buffer.from("MCowBQYDK2VwAyEAdf/XyHdYIM4CHZXFtKvJEv5hIHLHC31VbsiWMG+uhbY=", "base64"), format: "der", type: "spki" });
console.log(verify(null, Buffer.from(JSON.stringify(ordena(env.lista))), chave, Buffer.from(env.assinatura, "base64")));
```
