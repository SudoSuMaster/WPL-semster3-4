WPL2 Levano Moermond - Hanze
# Team Verlofplanning Tool – Vergelijking per MoSCoW

## Context
Deze pagina bevat de volledige vergelijking van de onderzochte oplossingen voor de verlofplanner. De criteria zijn opgebouwd vanuit de functionele wensen, security-eisen, platform-ondersteuning en operationele randvoorwaarden.

## Legend
- ✅ = voldoet goed aan de eis.
- ⚠️ = gedeeltelijk of met omwegen.
- ❌ = voldoet niet of nauwelijks.

## Must Have – Functionaliteit
| Eis | OpenProject | Nextcloud | TimeOff.Management | ERPNext | OrangeHRM |
|---|---|---|---|---|---|
| Zelf verlof registreren | ⚠️ Workaround (issues/tasks) | ✅ Kalender events | ✅ Native feature | ✅ Native module | ✅ Native HRM feature |
| Eén kalenderoverzicht | ⚠️ Projectkalender | ✅ Native kalender | ✅ Team kalender | ✅ Team kalender | ✅ Team / absence calendar |
| Individueel overzicht | ⚠️ Filters nodig | ✅ Per gebruiker | ✅ Per medewerker | ✅ Per medewerker | ✅ Per medewerker |
| NL feestdagen | ❌ Handmatig | ✅ Eenvoudig toe te voegen | ⚠️ Handmatig toevoegen | ✅ Eenvoudig toe te voegen | ✅ Ondersteuning via HR configuratie |
| Webinterface | ✅ | ✅ | ✅ | ✅ | ✅ |

## Must Have – Security & Identity
| Eis | OpenProject | Nextcloud | TimeOff.Management | ERPNext | OrangeHRM |
|---|---|---|---|---|---|
| SSO (LDAP/OIDC) | ✅ | ✅ | ⚠️ Beperkt / via integratie | ✅ | ✅ |
| RBAC (user/admin) | ✅ Sterk | ⚠️ Basis | ⚠️ Basis (rollen aanwezig) | ✅ | ✅ Sterk |

## Must Have – Platform & Deployment
| Eis | OpenProject | Nextcloud | TimeOff.Management | ERPNext | OrangeHRM |
|---|---|---|---|---|---|
| Self-hosted | ✅ | ✅ | ✅ | ✅ | ✅ |
| Container support | ✅ | ✅ | ✅ | ✅ | ✅ |
| Harbor compatibel | ✅ | ✅ | ✅ | ✅ | ✅ |
| Kubernetes deployment | ✅ (Helm) | ⚠️ Complexer | ⚠️ Custom (geen officiële Helm) | ✅ (Helm) | ⚠️ Meestal custom / community |
| GitOps (ArgoCD) | ✅ Goed passend | ⚠️ Minder standaard | ⚠️ Handmatig inrichten | ✅ Goed passend | ⚠️ Mogelijk, maar niet standaard |

## Must Have – Data & Operations
| Eis | OpenProject | Nextcloud | TimeOff.Management | ERPNext | OrangeHRM |
|---|---|---|---|---|---|
| Persistent storage | ✅ DB | ✅ DB + files | ✅ DB | ✅ DB | ✅ DB |
| Backup/restore | ✅ | ✅ | ✅ | ✅ | ✅ |
| Resource limits | ✅ | ⚠️ Minder standaard | ⚠️ Zelf definiëren | ✅ | ⚠️ Deels custom |

## Should Have
| Eis | OpenProject | Nextcloud | TimeOff.Management | ERPNext | OrangeHRM |
|---|---|---|---|---|---|
| Week/maand view | ✅ | ✅ | ✅ | ✅ | ✅ |
| Categorieën afwezigheid | ⚠️ Custom fields | ✅ Kalender categorieën | ✅ Native | ✅ Native | ✅ Native |
| Excel import | ⚠️ Mogelijk | ⚠️ Beperkt | ⚠️ Beperkt | ✅ CSV/XLS mogelijk | ⚠️ Mogelijk via import / custom |
| Visueel beter dan Excel | ⚠️ Subjectief | ✅ | ✅ | ✅ | ✅ |
| Mobiel/responsive | ⚠️ Matig | ✅ | ✅ | ✅ | ✅ |

## Could Have
| Eis | OpenProject | Nextcloud | TimeOff.Management | ERPNext | OrangeHRM |
|---|---|---|---|---|---|
| iCal / Outlook export | ⚠️ Beperkt | ✅ | ⚠️ Beperkt | ✅ | ✅ |
| Notificaties | ✅ | ✅ | ✅ | ✅ | ✅ |
| Audit log | ✅ | ⚠️ Beperkt | ⚠️ Basis | ✅ | ✅ |
| Kleurcodering | ⚠️ Beperkt | ✅ | ✅ | ✅ | ✅ |
| Consignatie basis | ⚠️ Mogelijk | ❌ | ⚠️ Beperkt | ⚠️ Beperkt | ⚠️ Beperkt |

## Won’t Have (MVP)
| Eis | OpenProject | Nextcloud | TimeOff.Management | ERPNext | OrangeHRM |
|---|---|---|---|---|---|
| Automatische consignatieplanning | ❌ | ❌ | ❌ | ⚠️ Beperkt | ❌ |
| Verlofsaldo | ❌ | ❌ | ⚠️ Basis mogelijk | ✅ Native | ✅ Native |
| HR integraties | ❌ | ❌ | ❌ | ✅ Payroll / HR modules | ✅ Brede HRM-integraties |
| Payroll | ❌ | ❌ | ❌ | ✅ Native | ⚠️ Via HRM-suite |
| Volledig HR systeem | ❌ | ❌ | ❌ | ✅ | ✅ |
| Goedkeuringsflows | ⚠️ Beperkt | ❌ | ✅ Basis approval | ✅ Native | ✅ Native |

## Samenvattende score
| Categorie | Beste keuze | Waarom |
|---|---|---|
| Gebruiksgemak | Nextcloud | Snel bruikbaar en vertrouwd voor gebruikers. |
| Verlof-specifiek | TimeOff.Management | Gericht op verlofregistratie en compact. |
| Kubernetes / GitOps fit | OpenProject | Past sterk bij container- en platformaanpak. |
| RBAC / enterprise | OpenProject | Sterke rol- en rechtenstructuur. |
| Toekomst (uitbreidbaarheid) | OpenProject / ERPNext / OrangeHRM | Breder platform voor verdere groei. |

## Conclusie
| Scenario | Beste tool | Opmerking |
|---|---|---|
| Snel Excel vervangen | Nextcloud | Makkelijk inzetbaar, maar minder verlof-specifiek. |
| Beste match voor verlofregistratie | TimeOff.Management | Functioneel het meest direct voor deze use case. |
| Enterprise / platform engineering | OpenProject | Sterk in deployment, rechten en beheer. |
| Toekomst: consignatie en breder procesbeheer | OpenProject / ERPNext / OrangeHRM | Meer ruimte voor uitbreiding en procesgroei. |

## Korte toelichting
OrangeHRM is toegevoegd als bredere HRM-oplossing. De tool biedt sterkere HRM-functionaliteit dan TimeOff.Management en is daardoor interessant wanneer verlof onderdeel is van een bredere personeels- en beheeromgeving.

TimeOff.Management blijft het meest gericht op simpele en duidelijke verlofregistratie. OpenProject is vooral sterk wanneer de focus ligt op platformbeheer, governance en Kubernetes/GitOps-geschiktheid.

## Presentatielink
Voor de volledige onderbouwing kun je in je presentatie verwijzen naar deze pagina: `moscow.md`.
