---
title: Sidecar ou ambient?
description: Aprenda sobre os modos Dataplane do Istio e qual você deveria utilizar.
weight: 30
keywords: [sidecar, ambient]
owner: istio/wg-docs-maintainers-english
test: n/a
---

A service mesh criada pelo Istio é dividida em dois componentes: data plane e control plane.

O {{< gloss >}}data plane{{< /gloss >}} é o conjunto de proxies que interceptam e controlam toda a comunicação entre os microsserviços.
Também coletam e reportam a telemetria de toda a mesh.

O {{< gloss >}}control plane{{< /gloss >}} gerencia e configura os proxies integrados ao data plane.

Istio suporta dois modos do {{< gloss "data plane mode">}}data plane{{< /gloss >}}:

* **sidecar**, que realiza o deploy de um proxy Envoy em cada pod existente no seu cluster ou como um serviço sendo executado em VMs.
* **ambient**, que utiliza um proxy Envoy camada 4 por node e (opcionalmente) um proxy Envoy camada 7 por namespace.

Você pode escolher quais namespaces e workloads deseja para cada modo.

## Modo Sidecar

Istio foi construído no modelo baseado em sidecar desde o primeiro lançamento em 2017. O modelo Sidecar é bem compreendido e amplamente testado, porém traz um aumento no consumo de recursos e sobrecarga operacional.

* Toda aplicação executada terá um proxy Envoy {{< gloss "injection" >}}injetado{{< /gloss >}} como um sidecar
* Todos os proxies podem processar requisições de camada 4 e 7

## Modo Ambient

Lançado em 2022, o modo Ambiente foi construído para solucionar dificuldades reportadas pelos usuários do modo sidecar. A partir do Istio 1.22, o modo ambient foi declarado como pronto para produção para cenários de clusteres únicos.

* Todo o tráfego é passado através de um proxy camada 4
* Aplicações podem ter seu tráfego de camada 7 roteado por um proxy Envoy

## Escolhendo entre sidecar e ambient

Usuários frequentemente realizam o deployment de uma mesh para habilitar capacidades de zero-trust como um primeiro passo e posteriormente, habilitam capacidades extras de camada 7 conforme necessário.
O modo Ambient permite que esses usuários ignorem os custos adicionados pelo processamento em camada 7 quando não forem necessários.

<table>
  <thead>
    <tr>
      <td style="border-width: 0px"></td>
      <th><strong>Sidecar</strong></th>
      <th><strong>Ambient</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Gerenciamento de tráfego</th>
      <td>Contém todas as capacidades</td>
      <td>Contém todas as capacidades (requer o uso do waypoint)</td>
    </tr>
    <tr>
      <th>Segurança</th>
      <td>Contém todas as capacidades</td>
      <td>Contém todas as capacidades: criptografia e autorização em camada 4 no modo ambient. Requer waypoint para autorização de camada 7.</td>
    </tr>
    <tr>
      <th>Observabilidade</th>
      <td>Contém todas as capacidades</td>
      <td>Contém todas as capacidades: telemetria de camada 4 no modo ambient; observabilidade de camada 7 ao utilizar o waypoint</td>
    </tr>
    <tr>
      <th>Extensibilidade</th>
      <td>Contém todas as capacidades</td>
      <td>Através de <a href="/docs/ambient/usage/extend-waypoint-wasm">plugins WebAssembly</a> (requer waypoint)<br>A API EnvoyFilter não é suportada.</td>
    </tr>
    <tr>
      <th>Adicionando workloads na mesh</th>
      <td>Adicione uma label ao namespace e reinicie todos os pods para ter os sidecars injetados</td>
      <td>Adicione uma label ao namespace - sem necessidade de reiniciar os pods</td>
    </tr>
    <tr>
      <th>Deployment incremental</th>
      <td>Binário: sidecar é ou não injetado</td>
      <td>Gradual: camada 4 estás sempre ativo, camada 7 pode ser adicionado</td>
    </tr>
    <tr>
      <th>Gerenciamento de ciclo de vida</th>
      <td>Proxies gerenciados pelo desenvolvedor da aplicação</td>
      <td>Administrador da plataforma</td>
    </tr>
    <tr>
      <th>Utilização de recursos</th>
      <td>Desperdício; CPU e memória devem ser provisionados para o pior cenário de cada pod</td>
      <td>Proxies waypoint podem auto-escalar como qualquer outro deployment.<br>Um workload com múltiplas réplicas pode utilizar um waypoint, ao invés de cada um ter seu próprio sidecar.
      </td>
    </tr>
    <tr>
      <th>Média de custo do recurso</th>
      <td>Grande</td>
      <td>Pequena</td>
    </tr>
    <tr>
      <th>Latência média (p90/p99)</th>
      <td>0.63ms-0.88ms</td>
      <td>Ambient: 0.16ms-0.20ms<br />Waypoint: 0.40ms-0.50ms</td>
    </tr>
    <tr>
      <th>Pontos de processamento (camada 7)</th>
      <td>2 (sidecar de origem e destino)</td>
      <td>1 (waypoint de destino)</td>
    </tr>
    <tr>
      <th>Configuração em escala</th>
      <td>Requer <a href="/docs/ops/configuration/mesh/configuration-scoping/">a configuração do escopo de cada sidecar</a> para configuração reduzida</td>
      <td>Funciona sem configuração adicional</td>
    </tr>
    <tr>
      <th>Compatível com protocolos "server-first"</th>
      <td><a href="/docs/ops/deployment/application-requirements/#server-first-protocols">Requer configuração</a></td>
      <td>Sim</td>
    </tr>
    <tr>
      <th>Compatível com Kubernetes Jobs</th>
      <td>Dificultado pelo longo ciclo de vida do sidecar</td>
      <td>Transparente</td>
    </tr>
    <tr>
      <th>Modelo de segurança</th>
      <td>Mais Forte: cada workload tem suas próprias chaves</td>
      <td>Forte: cada agent tem apenas as chaves dos workloads daquele node</td>
    </tr>
    <tr>
      <th>Pod comprometido<br>fornece acesso às chaves da mesh</th>
      <td>Sim</td>
      <td>Não</td>
    </tr>
    <tr>
      <th>Suporte</th>
      <td>Estável, incluindo multi-cluster</td>
      <td>Estável, apenas para cluster único</td>
    </tr>
    <tr>
      <th>Plataformas comportadas</th>
      <td>Kubernetes (qualquer CNI)<br />Máquinas virtuais</td>
      <td>Kubernetes (qualquer CNI)</td>
    </tr>
  </tbody>
</table>

## Capacidades de camada 4 x camada 7

O uso de recursos para protocolos de processamento em camada 7 é consideravelmente mais alto do que o processamento em camada 4. Caso seja possível atender seu caso de uso com processamento em camada 4, sua service mesh pode acabar tendo custos muito mais baixos.

### Segurança

<table>
  <thead>
    <tr>
      <td style="border-width: 0px" width="20%"></td>
      <th width="40%">L4</th>
      <th width="40%">L7</th>
    </tr>
   </thead>
   <tbody>
    <tr>
      <th>Criptografia</th>
      <td>Todo o tráfego entre os pods é criptografado utilizando {{< gloss "mutual tls authentication" >}}mTLS{{< /gloss >}}.</td>
      <td>N/A&mdash; identidade dos serviços é baseada em TLS.</td>
    </tr>
    <tr>
      <th>Autenticação entre serviços</th>
      <td>{{< gloss >}}SPIFFE{{< /gloss >}}, através de certificados mTLS. Istio emite certificados X.509 que codificam a conta de serviço do pod.</td>
      <td>N/A&mdash; identidade dos serviços é baseada em TLS.</td>
    </tr>
    <tr>
      <th>Autorização entre serviços</th>
      <td>Autorização baseada em rede, utilização de políticas baseadas em identidade, ex:
        <ul>
          <li>A pode aceitar requisições apenas vindas de "10.2.0.0/16";</li>
          <li>A pode chamar B.</li>
        </ul>
      </td>
      <td>Política extensa, ex:
        <ul>
          <li>A pode executar um GET no endpoint /foo de B, apenas utilizando uma credencial válida que contém o escopo READ.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <th>Autenticação de usuário</th>
      <td>N/A&mdash; não existe controle por usuário.</td>
      <td>Autenticação local com JWTs e suporte para autenticação remota utilizando Oauth e OIDC.</td>
    </tr>
    <tr>
      <th>Autorização de usuário</th>
      <td>N/A&mdash;veja acima.</td>
      <td>Políticas entre serviços podem ser estendidas para garantir  <a href="/docs/reference/config/security/conditions/">autenticação de usuários com credenciais contendo campos como scopes, issuers, principal, audiences, e mais.</a><br />Autorização entre usuário e recurso pode ser implementada através de autorização externa, permitindo políticas por request com decisões guiadas por um componente externo, como o OPA.</td>
    </tr>
  </tbody>
</table>

### Observabilidade

<table>
  <thead>
    <tr>
      <td style="border-width: 0px" width="20%"></td>
      <th width="40%">L4</th>
      <th width="40%">L7</th>
    </tr>
   </thead>
   <tbody>
    <tr>
      <th>Logging</th>
      <td>Informações básicas de rede: 5-tuple de rede, bytes enviados/recebidos, etc. <a href="https://www.envoyproxy.io/docs/envoy/latest/configuration/observability/access_log/usage#command-operators">Veja a documentação do Envoy</a>.</td>
        <td><a href="https://www.envoyproxy.io/docs/envoy/latest/configuration/observability/access_log/usage#command-operators">Registro completo de metadados de requisição</a>, além das informações básicas de rede.</td>
          </tr>
          <tr>
        <th>Rastreamento</th>
        <td>Inexistente; possível eventualmente utilizando HBONE.</td>
        <td>O Envoy participa no rastreamento distribuído. <a href="/docs/tasks/observability/distributed-tracing/overview/">Veja a visão geral do Istio sobre rastreamento</a>.</td>
          </tr>
          <tr>
        <th>Métricas</th>
        <td>Apenas TCP (bytes enviados/recebidos, número de pacotes, etc.).</td>
        <td>Métricas RED de camada 7: taxa de requisições, taxa de erros, duração da requisição (latência).</td>
          </tr>
        </tbody>
      </table>

* Conexão entre sidecar e waypoint
* Multi-cluster
* Multi rede
* Suporte para VMs
