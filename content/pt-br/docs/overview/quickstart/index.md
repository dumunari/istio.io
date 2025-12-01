---
title: "Quickstart"
description: Aprenda como começar com uma simples instalação.
weight: 50
keywords: [introduction]
owner: istio/wg-docs-maintainers-english
skip_seealso: true
test: n/a
---

Obrigado pelo seu interesse no Istio!

Istio tem dois modos: **ambient** e **sidecar**.

* [Ambient](/pt-br/docs/overview/dataplane-modes/#ambient-mode) é o novo e melhorado modelo, criado para endereçar problemas conhecidos do modelo sidecar. No modo ambient, um tunnel seguro é criado em cada node, permitindo que você habilite todas as funcionalidades mais avançadas conforme as configura, normalmente baseada em namespaces.
* [Sidecar](/pt-br/docs/overview/dataplane-modes/#sidecar-mode) é o modo tradicional de configuração da service mesh, popularizado pelo istio em 2017. Neste modelo, um proxy é executado junto de cada pod ou workload dentro do cluster Kubernetes.

Grande parte do esforço da comunidade do Istio está sendo direcionado para melhorar o modo ambient, mesmo que o modelo baseado em sidecars continue disponível. Novas funcionalidades devem ser integradas nos dois modelos.

Geralmente, **recomendamos que novos usuários iniciem com o modo ambient**. É mais rápido, barato e fácil de gerenciar. Existem [casos de uso avançados](/pt-br/docs/overview/dataplane-modes/#unsupported-features) que ainda requerem o uso do modo sidecar, porém essa lista está cada vez menor.

<div style="text-align: center;">
  <div style="display: inline-block;">
    <a href="/docs/ambient/getting-started"
       style="display: inline-block; min-width: 18em; margin: 0.5em;"
       class="btn btn--secondary"
       id="get-started-ambient">Comece com o modo ambient</a>
    <a href="/docs/setup/getting-started"
       style="display: inline-block; min-width: 18em; margin: 0.5em;"
       class="btn btn--secondary"
       id="get-started-sidecar">Comece com o modo sidecar</a>
  </div>
</div>
