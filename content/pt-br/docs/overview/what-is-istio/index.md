---
title: What is Istio?
description: Find out what Istio can do for you.
weight: 10
keywords: [introduction]
owner: istio/wg-docs-maintainers-english
test: n/a
---

Istio é uma solução de service mesh open source que incrementa as capacidades de aplicações distribuídas. Suas capacidades poderosas fornecem uma forma única e mais eficiente de proteger, conectar e monitorar seus serviços. Balanceamento de carga, autenticação entre serviços e monitoramento, são todas capacidades nativas do Istio, de fácil configuração. Tenha:

* Comunicação segura entre serviços em um cluster com criptografia baseada em mTLS, autenticação e autorização baseadas em identidade
* Balanceamento de carga automático para tráfego HTTP, gRPC, WebSocket e TCP
* Controle de comportamento do tráfego com regras de roteamento avançadas, retries, failovers e injeção de falhas
* Uma camada de política integrável e APIs de configuração que suportam controles de acesso, rate limits e quotas
* Métricas, logs e traces automáticos para todo o tráfego dentro de um cluster, incluindo ingress e egress

Istio foi criado para ser extensível e para lidar com uma gama de necessidades. Seu {{< gloss >}}control plane{{< /gloss >}} é executado no Kubernetes, as aplicações que estão sendo executadas no cluster podem ser adicionadas à mesh, [outros clusters também podem ser integrados](/pt-br/docs/ops/deployment/deployment-models/), ou até [VMs e outros endpoints](/pt-br/docs/ops/deployment/vm-architecture/) que estiverem sendo executados fora do cluster.

O grande ecosistema de contribuidores, parceiros, integrações e distribuidores popularizam e evoluem o Istio para sua variedade de cenários. Você pode instalá-lo ou pode utilizar de um [grande número de parceiros](/pt-br/about/ecosystem) que tem produtos que integram com a plataforma e a gerenciam para você.

## Como funciona

Istio utiliza um proxy para interceptar todo o tráfego de rede, habilitando uma grande gama de funcionalidades para suas aplicações baseadas nas configurações que você escolher.

O control plane gere as configurações desejadas e as características dos serviços, dinamicamente programando os proxies, os atualizando conforme suas regras e ambiente são alterados.

O data plane é a comunicação entre os serviços. Sem uma service mesh, a rede não compreende o tráfego que é enviado e não pode tomar decisões baseadas no tipo daquele tráfego, ou de quem e para quem ele é destinado.

Istio suporta dois modos para o data plane:

* **sidecar**, que executa um proxy Envoy junto de cada pod que você executar em seu cluster, ou executando serviços em uma VM.
* **ambient**, que utiliza um proxy de camdada 6 por node e opcionalmente um proxy Envoy de camada 7 por namespace.

[Avalie qual modo é o melhor para você](/pt-br/docs/overview/dataplane-modes/).
