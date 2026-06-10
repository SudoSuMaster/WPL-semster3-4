(Dump i.v.m. dat we alle documenten op gitlab storen en niet meer in word of excel bestanden)

# Technisch Ontwerp – Verlofplanner op Kubernetes

## Doel en context
Dit document beschrijft het technische ontwerp van een self-hosted verlofplanner die binnen een Kubernetes-platform kan worden uitgerold. Het ontwerp is bedoeld als bijlage bij het portfolio en dient als bewijsstuk voor het systematisch uitwerken van een technische oplossing volgens een ontwerpaanpak waarin probleem, eisen, oplossingsrichting en technische uitwerking samenkomen [1][2][3].

Binnen dit technisch ontwerp is gekozen voor een stack die sterk aansluit op het CNCF-ecosysteem. De Cloud Native Computing Foundation vormt het vendor-neutrale thuis voor veel cloud-native projecten, waaronder Kubernetes en Helm, waardoor veel van de gebruikte tools uit dit ontwerp uit dezelfde open source context komen [4][5][18].

De opbouw van dit document sluit aan op de SLO-ontwerpbenadering, waarin eerst het ontwerpprobleem wordt geanalyseerd, daarna eisen en randvoorwaarden worden vastgesteld, vervolgens oplossingsrichtingen worden uitgewerkt en ten slotte een ontwerp wordt beoordeeld op geschiktheid [1][2][3]. Voor de technische invulling van het ontwerp is gebruikgemaakt van Kubernetes-gerelateerde bronnen over GitOps, Helm, Kustomize, Harbor, persistente opslag en backup/restore [4][5][6][7][8].

## Waarom deze bronnen zijn gebruikt
De gebruikte bronnen zijn bewust gekozen om verschillende onderdelen van het technische ontwerp te onderbouwen. De SLO-bronnen zijn gebruikt om de structuur van het ontwerpproces vast te leggen en het document logisch op te bouwen [1][2][3].

De bronnen over Argo CD, Helm en Kustomize zijn gebruikt om de technische keuzes rondom GitOps, charts en omgevingsspecifieke configuratie te motiveren [4][5][6]. De Harbor-bronnen zijn gebruikt om imagebeheer en beveiliging van containerimages te onderbouwen [7][15]. De Kasten-bron is gebruikt om de backup- en restore-aanpak voor Kubernetes-workloads te verklaren [8].

De bronnen over persistent volumes en persistent volume claims zijn gebruikt om duidelijk te maken waarom opslag los van de pods moet worden ingericht [13][14]. De bronnen over TimeOff.Management en OrangeHRM zijn gebruikt om de softwarevarianten inhoudelijk te vergelijken en de gekozen ontwerpkeuzes te onderbouwen [11][12].

## Probleemanalyse
De opdracht is het ontwerpen van een technisch realiseerbare verlofplanner voor intern gebruik binnen een beheerste platformomgeving. De oplossing moet self-hosted kunnen draaien, via de browser toegankelijk zijn en passen binnen een Kubernetes-landschap waarin deployments herhaalbaar, beheerbaar en veilig uitgevoerd kunnen worden [4][9][10].

Vanuit de analyse zijn twee softwarekandidaten onderzocht: TimeOff.Management en OrangeHRM. TimeOff.Management sluit functioneel goed aan op verlofregistratie en teamoverzicht, terwijl OrangeHRM interessanter is als bredere HRM-oplossing met extra uitbreidingsmogelijkheden [11][12]. Beide applicaties vragen om een technisch ontwerp waarin niet alleen de software, maar ook deployment, configuratie, opslag, beveiliging en herstelbaarheid zijn opgenomen [4][8][13].

## Eisen en randvoorwaarden
Op basis van de ontwerpfase zijn de volgende technische eisen en randvoorwaarden opgesteld [1][2]:

- De applicatie moet self-hosted zijn en binnen Kubernetes kunnen draaien [4].
- De deployment moet reproduceerbaar en beheerbaar zijn via Infrastructure as Code [9][10].
- De configuratie moet per omgeving aanpasbaar zijn zonder de basisdeployment opnieuw op te bouwen [5][6].
- Toegang van buitenaf moet via Ingress en TLS geregeld kunnen worden [4].
- Stateful data moet persistent opgeslagen worden via Kubernetes-opslagconstructies zoals PersistentVolumeClaims [13][14].
- Images moeten centraal beheerd en gecontroleerd aangeboden kunnen worden via een interne registry [7][15].
- Herstel van data en applicatiestatus moet mogelijk zijn via een backup- en restore-oplossing [8].

## Ontwerpaanpak
Voor dit ontwerp is gekozen voor een platformgerichte aanpak. Niet de applicatie zelf staat centraal, maar de manier waarop een applicatie als beheersbare dienst in Kubernetes wordt geplaatst. Daarmee wordt het ontwerp minder afhankelijk van één specifieke softwarekeuze en beter herbruikbaar voor meerdere oplossingsrichtingen [2][3].

De technische basis van het ontwerp bestaat uit GitOps met Argo CD, packaging met Helm charts, configuratiebeheer via values-bestanden en waar nodig aanvullende overlays met Kustomize. Argo CD gebruikt Git als bron van waarheid en synchroniseert de gewenste toestand van applicaties continu naar Kubernetes [4]. Helm bundelt Kubernetes-resources in charts en maakt herhaalbare installatie en upgrades mogelijk [5][16]. Kustomize ondersteunt declaratieve aanpassingen op omgevingsniveau zonder de basisresources te wijzigen [6][17].

## Platformarchitectuur
De platformarchitectuur bestaat uit meerdere lagen. In Git worden de chartdefinities, values-bestanden en eventuele overlays opgeslagen. Argo CD leest deze configuratie uit en zet de gewenste toestand om naar deployments in het Kubernetes-cluster [4]. Containerimages worden geleverd vanuit Harbor, dat artifacts opslaat en image scanning ondersteunt [7][15].

Binnen het cluster draaien de applicatiecomponenten als Deployments en worden zij ontsloten via Services en Ingress. Voor stateful data wordt gebruikgemaakt van PersistentVolumeClaims. Voor backup en herstel van applicatiedata en volumes is Kasten opgenomen in het ontwerp, omdat deze oplossing specifiek is gericht op Kubernetes backup, restore en disaster recovery [8][13][14].

## Softwareontwerp TimeOff.Management
TimeOff.Management is in dit ontwerp de eerste oplossingsrichting. Functioneel is deze applicatie gericht op afwezigheidsregistratie, teamoverzicht en verlofaanvragen, waardoor zij goed aansluit op het primaire doel van de opdracht [11]. De softwarearchitectuur is relatief compact en bestaat uit een webapplicatiecontainer, service, ingress en persistente opslag voor de applicatiedata [11].

De deployment van TimeOff.Management wordt ontworpen als een Helm chart. De chart bevat de Kubernetes-resources voor Deployment, Service, Ingress en PersistentVolumeClaim. In het values-bestand worden de omgevingsspecifieke instellingen vastgelegd, zoals image, hostnaam, replica-aantal, storagegrootte en omgevingsvariabelen [5][16][9]. Hierdoor wordt de software niet handmatig, maar via Infrastructure as Code gedeployed en geconfigureerd [9][10].

Voor de softwarearchitectuur van TimeOff.Management geldt dat de browsergebruiker via Traefik Ingress de applicatie bereikt. De request wordt doorgestuurd naar een Kubernetes Service, die verkeer routeert naar de TimeOff.Management Deployment. De applicatie schrijft de data weg naar een gemounte persistente opslaglocatie, zodat gegevens behouden blijven bij een herstart van de pod [13][14].

Een belangrijk aandachtspunt in dit ontwerp is de identity-koppeling. In de onderzochte situatie bleek LDAP-authenticatie lastig toepasbaar, omdat de applicatie gebruikers koppelt op basis van e-mail en wachtwoord binnen een multi-tenant model [11]. Daarmee is TimeOff.Management architectonisch eenvoudig inzetbaar, maar minder passend wanneer identity-integratie op basis van bestaande gebruikersstructuren een harde eis is.

## Tekening TimeOff.Management
Onderstaande tekening toont het softwareontwerp van TimeOff.Management. Deze is als eerste uitgewerkt, omdat deze applicatie het dichtst aansloot op de oorspronkelijke functionele vraag.

```mermaid
flowchart TD
    A[Gebruiker / Browser] --> B[Ingress / Traefik]
    B --> C[Service timeoff]
    C --> D[Deployment timeoff]
    D --> E[Container tigron/timeoff-management]
    E --> F[PVC timeoff-pvc]
    E --> G[Config uit values.yaml]
    G --> G1[image repository en tag]
    G --> G2[replicaCount]
    G --> G3[ingress host en TLS]
    G --> G4[persistence enabled, size, storageClass]
    G --> G5[securityContext]
    G --> G6[resources requests en limits]
    G --> G7[env vars]
    G7 --> G71[NODE_ENV=production]
    G7 --> G72[LDAP enabled]
    G7 --> G73[LDAP url]
    G7 --> G74[LDAP bindDN]
    G7 --> G75[LDAP searchBase]
    G7 --> G76[LDAP mail attribute]
    G7 --> G77[LDAP username attribute]
    G7 --> G78[secret refs voor bind password]
    D --> H[Pod security]
    H --> H1[runAsNonRoot]
    H --> H2[readOnlyRootFilesystem optioneel]
    H --> H3[allowPrivilegeEscalation false]
    H --> H4[seccompProfile RuntimeDefault]
    H --> H5[capabilities drop all]
    D --> I[NetworkPolicy]
    I --> I1[alleen ingress controller naar app]
    I --> I2[alleen app naar LDAP en cluster-dns]
    I --> I3[egress beperken waar mogelijk]
    J[Git repository] --> K[Helm chart]
    K --> G
    L[Argo CD] --> K
    M[Harbor registry] --> E
    N[LDAP / Active Directory] --> E
    O[Secret] --> G78
```

### Uitleg bij de tekening
De tekening laat zien dat TimeOff.Management via een Helm chart wordt gedeployed en dat de instelbare configuratie in `values.yaml` wordt vastgelegd. Daardoor kunnen imagegegevens, replica’s, ingress, opslag en security per omgeving worden aangepast zonder de chartstructuur te wijzigen [5][6][9].

De LDAP-configuratie is in het ontwerp opgenomen als configureerbare softwarelaag. Daarbij worden instellingen zoals LDAP URL, bind-DN, search base en attribuutmapping via values en secrets doorgegeven aan de deployment, zodat identity-integratie niet handmatig in het cluster hoeft te worden ingesteld [11].

De PVC in de tekening staat voor persistente opslag van de applicatiedata. Persistent volumes en claims zorgen ervoor dat gegevens behouden blijven als pods opnieuw starten of vervangen worden [13][14].

De security-instellingen in de tekening zijn onderdeel van het softwareontwerp, zodat de applicatie niet alleen functioneel draait maar ook volgens veiligere Kubernetes-principes wordt ingericht. Voorbeelden zijn een beperkte security context, secrets voor gevoelige configuratie en een netwerkbeperking via NetworkPolicies [9][10].

### Voorbeeldstructuur values.yaml
```yaml
image:
  repository: tigron/timeoff-management
  tag: latest

replicaCount: 1

ingress:
  enabled: true
  className: traefik
  host: timeoff.test.bcp.mindef.nl
  tls:
    enabled: true
    secretName: timeoff-tls

persistence:
  enabled: true
  existingClaim: timeoff-pvc
  size: 1Gi
  mountPath: /app/db

ldap:
  enabled: true
  url: ldaps://ldap.example.local:636
  bindDN: CN=svc-timeoff,OU=Service Accounts,DC=example,DC=local
  searchBase: OU=Users,DC=example,DC=local
  userFilter: (mail={{username}})
  mailAttribute: mail
  usernameAttribute: sAMAccountName
  existingSecret: timeoff-ldap-secret

security:
  runAsNonRoot: true
  allowPrivilegeEscalation: false
  readOnlyRootFilesystem: false
  seccompProfile: RuntimeDefault
```

## Softwareontwerp OrangeHRM
OrangeHRM is de tweede oplossingsrichting en is ontworpen als uitgebreidere HRM-oplossing. De applicatie ondersteunt HR-functionaliteit rond medewerkersbeheer en verlof, maar in de community edition zijn functionele beperkingen aanwezig ten opzichte van uitgebreidere commerciële varianten [12]. Binnen dit technisch ontwerp is OrangeHRM opgenomen als softwarevariant met een zwaardere maar meer uitbreidbare architectuur.

De softwarearchitectuur van OrangeHRM bestaat uit ten minste twee hoofdcomponenten: de OrangeHRM-webapplicatie en een MariaDB-database. De webapplicatie draait als Kubernetes Deployment en communiceert met MariaDB via een interne Service. Voor de database is persistente opslag nodig, omdat databasegegevens behouden moeten blijven buiten de levensduur van pods [13][14].

Net als bij TimeOff.Management wordt ook OrangeHRM via een Helm chart ontworpen. In de chart worden de resources voor namespace, deployment, service, ingress en databasecomponenten vastgelegd. Via values-bestanden worden imageversies, database-instellingen, hostnames, resources en opslagparameters geconfigureerd [5][16]. Hierdoor ontstaat ook hier een deploymentmodel waarin software en configuratie als code zijn vastgelegd [9][10].

In het ontwerp is meegenomen dat OrangeHRM LDAP kan configureren, maar dat in de onderzochte situatie de connectie wel gebruikers kon vinden terwijl de synchronisatie nog niet correct werkte [12]. Dat maakt OrangeHRM technisch interessanter voor verdere validatie, maar nog niet zonder meer direct productiegeschikt binnen de onderzochte context.

### Tekening OrangeHRM
De OrangeHRM-tekening is later toegevoegd als aanvullende ontwerpvariant, zodat ook deze oplossingsrichting zichtbaar is binnen hetzelfde technisch ontwerp.

```mermaid
flowchart TD
    A[Gebruiker / Browser] --> B[Ingress / Traefik]
    B --> C[Service orangehrm]
    C --> D[Deployment orangehrm]
    D --> E[Container orangehrm/orangehrm:5.8.1]
    D --> F[MariaDB Deployment]
    F --> G[PVC mariadb-pvc]
    D --> H[Config uit values.yaml]
    H --> H1[image repository en tag]
    H --> H2[replicaCount]
    H --> H3[ingress host en TLS]
    H --> H4[DB_HOST]
    H --> H5[DB_PORT]
    H --> H6[DB_NAME]
    H --> H7[DB_USER]
    H --> H8[DB_PASS via Secret]
    H --> H9[LDAP enabled]
    H --> H10[LDAP url]
    H --> H11[LDAP bindDN]
    H --> H12[LDAP searchBase]
    H --> H13[LDAP attribute mapping]
    D --> I[Pod security]
    I --> I1[runAsNonRoot]
    I --> I2[allowPrivilegeEscalation false]
    I --> I3[seccompProfile RuntimeDefault]
    J[Git repository] --> K[Helm chart]
    K --> H
    L[Argo CD] --> K
    M[Harbor registry] --> E
    N[Secret] --> H8
    O[LDAP / Active Directory] --> D
```

## Vergelijking van de oplossingsrichtingen
| Aspect | TimeOff.Management | OrangeHRM |
|---|---|---|
| Primaire focus | Verlof en afwezigheid [11] | Brede HRM-functionaliteit [12] |
| Architectuur | Eenvoudiger, één hoofdapplicatie met persistente opslag [11][13] | Uitgebreider, webapplicatie plus MariaDB [12][13] |
| Deploymentmodel | Helm chart met values [5][16] | Helm chart met values [5][16] |
| IaC-geschiktheid | Goed toepasbaar [9][10] | Goed toepasbaar [9][10] |
| Identity-aansluiting | Beperkt door e-mailgebaseerde LDAP-koppeling [11] | Potentieel beter, maar synchronisatie nog niet stabiel [12] |
| Uitbreidbaarheid | Beperkter [11] | Groter [12] |

## Technische ontwerpkeuzes
De kern van het technische ontwerp is dat deployment en configuratie volledig declaratief worden vastgelegd. Hiervoor worden Helm charts gebruikt als verpakkingsmechanisme en values-bestanden voor omgevingsspecifieke instellingen [5][16]. Argo CD zorgt ervoor dat de in Git vastgelegde gewenste toestand automatisch wordt toegepast op het cluster, waardoor wijzigingen controleerbaar en reproduceerbaar blijven [4][18].

Daarnaast is gekozen voor centrale imagelevering via Harbor. Harbor biedt opslag van artifacts en ondersteunt beveiligingsmaatregelen zoals scanning en policy-gestuurd beheer [7][15]. Voor dataopslag wordt gebruikgemaakt van PVC’s, omdat deze nodig zijn om stateful data los te koppelen van de levensduur van containers [13][14]. Voor operationele continuïteit is Kasten opgenomen als voorziening voor backup en restore [8].

## Beoordeling van het ontwerp
Binnen dit technisch ontwerp is TimeOff.Management de meest directe oplossing voor het oorspronkelijke vraagstuk van verlofregistratie. De applicatie is kleiner van opzet en eenvoudiger te modelleren als Kubernetes-workload [11]. Tegelijk laat de analyse zien dat identity-integratie een belangrijk knelpunt vormt, wat de geschiktheid binnen de onderzochte organisatiecontext beperkt [11].

OrangeHRM is architectonisch zwaarder, maar beter schaalbaar als bredere HRM-oplossing. De aanwezigheid van een aparte databasecomponent en de mogelijkheid tot verdere uitbreiding maken deze oplossingsrichting interessanter vanuit softwarearchitectuur, ook al zijn er nog functionele en integratiegerichte aandachtspunten [12][13]. Daarmee is het technisch ontwerp geschikt als basis voor verdere realisatie en validatie in een volgende projectfase.

## Conclusie
Dit document vormt een technisch ontwerp voor de inzet van een verlofplanner binnen Kubernetes en is opgesteld volgens een gestructureerde ontwerpaanpak gebaseerd op SLO. Het ontwerp beschrijft het probleem, de eisen, de platformarchitectuur en twee softwarevarianten, uitgewerkt volgens principes van GitOps en Infrastructure as Code [1][2][3][4][9].

Als bewijsstuk laat dit ontwerp zien dat de oplossing niet alleen functioneel is bekeken, maar ook technisch is uitgewerkt op het niveau van deployment, configuratie, opslag, identity, beheer en herstelbaarheid. Daarmee is het document geschikt om als bijlage te gebruiken en er in het portfolio expliciet naar te verwijzen.

## Bronvermelding
1. SLO. *A6: Ontwerpen*. [https://www.slo.nl/handreikingen/havo-vwo/handreiking-se-nlt/examenprogramma/domein-a/a6-ontwerpen/](https://www.slo.nl/handreikingen/havo-vwo/handreiking-se-nlt/examenprogramma/domein-a/a6-ontwerpen/)
2. SLO. *Componenten van W&T - Onderzoeken en ontwerpen*. [https://www.slo.nl/thema/meer/wetenschap/componenten/onderzoeken/](https://www.slo.nl/thema/meer/wetenschap/componenten/onderzoeken/)
3. SLO. *Ontwerpen*. [https://www.slo.nl/thema/vakspecifieke-thema/natuur-techniek/kennisbasis/werkwijzen/ontwerpen/](https://www.slo.nl/thema/vakspecifieke-thema/natuur-techniek/kennisbasis/werkwijzen/ontwerpen/)
4. Argo CD Documentation. *Overview*. [https://argo-cd.readthedocs.io](https://argo-cd.readthedocs.io)
5. Helm. *Helm Charts*. [https://helm.sh](https://helm.sh)
6. Kustomize. *Kubernetes native configuration management*. [https://kustomize.io](https://kustomize.io)
7. Harbor. *Harbor*. [https://goharbor.io](https://goharbor.io)
8. Veeam Kasten for Kubernetes. *Overview*. [https://docs.kasten.io/8.5.7/](https://docs.kasten.io/8.5.7/)
9. Octopus Deploy. *IaC in Kubernetes: 6 Tools, Tutorial and Tips for Success*. [https://octopus.com/devops/infrastructure-as-code/iac-kubernetes/](https://octopus.com/devops/infrastructure-as-code/iac-kubernetes/)
10. Palo Alto Networks. *Kubernetes and Infrastructure as Code*. [https://www.paloaltonetworks.com/cyberpedia/kubernetes-infrastructure-as-code](https://www.paloaltonetworks.com/cyberpedia/kubernetes-infrastructure-as-code)
11. GitHub. *TimeOff.Management*. [https://github.com/timeoff-management/timeoff-management-application](https://github.com/timeoff-management/timeoff-management-application)
12. GitHub. *OrangeHRM Community Edition*. [https://github.com/orangehrm/orangehrm](https://github.com/orangehrm/orangehrm)
13. Simplyblock. *Kubernetes Persistent Volumes - Best Practices & Guide*. [https://simplyblock.io/blog/kubernetes-persistent-volumes-how-to-best-practices/](https://simplyblock.io/blog/kubernetes-persistent-volumes-how-to-best-practices/)
14. NetApp. *Kubernetes Persistent Volume Claims Explained*. [https://www.netapp.com/learn/cvo-blg-kubernetes-persistent-volume-claims-explained/](https://www.netapp.com/learn/cvo-blg-kubernetes-persistent-volume-claims-explained/)
15. Harbor Docs. *Vulnerability Scanning*. [https://goharbor.io/docs/2.0.0/administration/vulnerability-scanning/](https://goharbor.io/docs/2.0.0/administration/vulnerability-scanning/)
16. Helm Docs. *helm install*. [https://helm.sh/docs/helm/helm_install/](https://helm.sh/docs/helm/helm_install/)
17. Kubernetes SIGs. *kustomize*. [https://github.com/kubernetes-sigs/kustomize](https://github.com/kubernetes-sigs/kustomize)
18. Akuity. *GitOps Best Practices*. [https://akuity.io/blog/gitops-best-practices-whitepaper](https://akuity.io/blog/gitops-best-practices-whitepaper)
