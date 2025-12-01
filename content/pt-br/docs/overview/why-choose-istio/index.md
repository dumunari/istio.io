---
title: Why choose Istio?
description: Compare Istio to other service mesh solutions.
weight: 20
keywords: [comparison]
owner: istio/wg-docs-maintainers-english
test: n/a
---

Istio foi pioneiro no conceito de service mesh baseado em sidecar quando foi lançado em 2017. Desde o início, o projeto incluía os recursos que viriam a definir um service mesh, incluindo mutual TLS baseado em padrões para redes zero-trust, roteamento inteligente de tráfego e observabilidade por meio de métricas, logs e rastreamento.

Desde então, o projeto impulsionou avanços no espaço de mesh, incluindo [topologias multi-cluster e multi-rede](/pt-br/docs/ops/deployment/deployment-models/), [extensibilidade via WebAssembly](/pt-br/docs/concepts/wasm/), o [desenvolvimento da Kubernetes Gateway API](/pt-br/blog/2022/gateway-api-beta/) e a remoção da infraestrutura de mesh do caminho dos desenvolvedores com o [modo ambient](/pt-br/docs/ambient/overview/).

Aqui estão algumas razões pelas quais achamos que você deve usar Istio como seu service mesh.

## Simples e poderoso

O Kubernetes tem centenas de recursos e dezenas de APIs, mas você pode começar a usá-lo com apenas um comando. Construímos o Istio da mesma forma. A divulgação progressiva significa que você pode usar um pequeno conjunto de APIs e só ativar opções mais avançadas se precisar delas. Outros service meshes “simples” passaram anos tentando alcançar o conjunto de recursos que o Istio já tinha no primeiro dia.

É melhor ter um recurso e não precisar dele, do que precisar e não tê-lo!

## O proxy Envoy {#envoy}

Desde o início, o Istio foi alimentado pelo proxy {{< gloss >}}Envoy{{< /gloss >}}, um proxy de serviço de alto desempenho criado inicialmente pela Lyft. O Istio foi o primeiro projeto a adotar o Envoy, e [a equipe do Istio foi a primeira a contribuir externamente](https://eng.lyft.com/envoy-7-months-later-41986c2fd443). O Envoy se tornaria [o balanceador de carga que alimenta o Google Cloud](https://cloud.google.com/load-balancing/docs/https), além de ser o proxy de quase todas as outras plataformas de service mesh.

O Istio herda todo o poder e flexibilidade do Envoy, incluindo extensibilidade de nível mundial usando WebAssembly, que foi [desenvolvida no Envoy pela equipe do Istio](/pt-br/blog/2020/wasm-announce/).

## Comunidade

Istio é um verdadeiro projeto comunitário. Em 2023, havia 10 empresas que fizeram mais de 1.000 contribuições cada para o Istio, sem nenhuma empresa ultrapassar 25%. ([Veja os números aqui](https://istio.devstats.cncf.io/d/5/companies-table?var-period_name=Last%20year&var-metric=contributions&orgId=1)).

Nenhum outro service mesh possui o mesmo nível de apoio da indústria que o Istio.

## Pacotes

Disponibilizamos versões binárias estáveis para todos, em cada lançamento, e nos comprometemos a continuar fazendo isso. Publicamos patches de segurança gratuitos e regulares para nossa [versão mais recente e várias versões anteriores](/pt-br/docs/releases/supported-releases/). Muitos de nossos fornecedores oferecem suporte a versões mais antigas, mas acreditamos que depender de um fornecedor não deve ser um requisito para ter segurança em um projeto open source estável.

## Alternativas consideradas

Um bom documento de design inclui uma seção sobre alternativas consideradas e, por fim, rejeitadas.

### Por que "usar eBPF"?

Nós usamos – quando é apropriado! O Istio pode ser configurado para usar {{< gloss >}}eBPF{{< /gloss >}} para [rotear tráfego dos pods para os proxies](/pt-br/blog/2022/merbridge/). Isso mostra um pequeno aumento de desempenho em comparação ao uso de `iptables`.

Why not use it for everything? No-one does, because no-one actually can.

eBPF é uma máquina virtual que roda dentro do kernel Linux. Ele foi projetado para funções garantidas a serem concluídas com uso limitado de computação, evitando desestabilizar o kernel, como funções que realizam roteamento simples de tráfego L3 ou observabilidade de aplicações. Ele não foi projetado para funções longas ou complexas como as encontradas no Envoy: é por isso que sistemas operacionais têm [user space](https://en.wikipedia.org/wiki/User_space_and_kernel_space)! Os mantenedores de eBPF teorizaram que ele poderia eventualmente ser estendido para suportar um programa tão complexo quanto o Envoy, mas isso é um projeto científico e improvável que tenha aplicabilidade prática no mundo real.

Outros meshes que afirmam “usar eBPF” na verdade usam um proxy Envoy por nó, ou outras ferramentas em espaço de usuário, para grande parte de sua funcionalidade.

### Por que não usar um proxy por nó?

Envoy não é inerentemente multi-tenant. Como resultado, temos grandes preocupações de segurança e estabilidade ao misturar regras complexas de processamento de tráfego L7 de múltiplos tenants não restritos em uma instância compartilhada. Como o Kubernetes, por padrão, pode agendar um pod de qualquer namespace em qualquer nó, o nó não é um limite apropriado para tenancy. Planejamento e atribuição de custos também são grandes problemas, já que o processamento L7 custa muito mais do que L4.

No modo ambient, limitamos estritamente nosso proxy ztunnel ao processamento L4 — [assim como o kernel Linux](https://blog.howardjohn.info/posts/ambient-spof/). Isso reduz significativamente a superfície de vulnerabilidade e nos permite operar com segurança um componente compartilhado. O tráfego é então encaminhado para proxies Envoy que operam por namespace, garantindo que nenhum proxy Envoy seja multi-tenant.

## Eu já tenho um CNI. Por que preciso do Istio?

Hoje, alguns plugins CNI estão começando a oferecer funcionalidades semelhantes a service mesh como um complemento que fica sobre sua própria implementação CNI. Por exemplo, eles podem implementar seus próprios esquemas de criptografia para tráfego entre nós ou pods, identidade de workloads ou algum nível de política em nível de transporte, redirecionando tráfego para um proxy L7. Esses complementos de service mesh são não padronizados e, portanto, só funcionam sobre o CNI que os fornece. Eles também oferecem conjuntos de recursos variados. Por exemplo, soluções baseadas em Wireguard não podem ser compatíveis com FIPS.

Por esse motivo, o Istio implementou seu componente de túnel zero-trust (ztunnel), que fornece essa funcionalidade de forma transparente e eficiente usando protocolos de criptografia comprovados e padrão da indústria. [Saiba mais sobre ztunnel](/pt-br/docs/ambient/overview).

O Istio foi projetado para ser um service mesh que oferece uma implementação consistente, altamente segura, eficiente e compatível com padrões, proporcionando um [conjunto poderoso de políticas L7](/pt-br/docs/concepts/security/#authorization), [identidade de workload independente de plataforma](/pt-br/docs/concepts/security/#istio-identity) e [uso de protocolos mTLS comprovados na indústria](/pt-br/docs/concepts/security/#mutual-tls-authentication) — em qualquer ambiente, com qualquer CNI, ou até mesmo entre clusters com CNIs diferentes.