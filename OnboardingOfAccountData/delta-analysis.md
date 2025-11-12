# Unterschied: Account Object in Emarsys vs. SAP Sales Cloud V2

Diese Gegenüberstellung beschreibt die Unterschiede zwischen dem **Account Object** (Kunden-/Unternehmenskonto) in der **SAP Sales Cloud Version 2 (V2)** und dem **Corporate Account** Object in **Emarsys**.

Der zentrale Unterschied liegt im **Primärzweck** der jeweiligen Systeme, der die Datenstruktur grundlegend definiert.

## I. Primäre Funktion und Datenfokus

| Kriterium | SAP Sales Cloud V2 (Source) | Emarsys (Target) |
| :--- | :--- | :--- |
| **Primärer Zweck** | **B2B Sales Execution & Master Data Management** | **B2C/B2B Marketing & Customer Engagement** |
| **Rolle des Objekts** | **Zentrale Entität** für alle Verkaufsaktivitäten (Opportunities, Quotes, Aktivitäten). | **Zentrale Entität** für Account-basierte Segmentierung und Personalisierung. |
| **Datenfokus** | Tiefgehend, strukturiert, Fokus auf **Sales-Prozesse, Beziehungen, Finanzdaten, Hierarchie**. | Flach, Fokus auf **Marketing-Attribute, Präferenzen, E-Mail-Kommunikation**. |
| **Datenvolumen** | Geringere Anzahl an Accounts, aber **hohe Datentiefe** pro Account. | Potenziell höhere Anzahl an Kontakten, **geringere Datentiefe** auf Account-Ebene. |

---

## II. Spezifische Strukturunterschiede

Die SAP Sales Cloud V2 speichert mehr relationale und prozessrelevante Daten, die in Emarsys entweder nicht benötigt oder als einfache Custom Fields abgebildet werden.

| Datenbereich | SAP Sales Cloud V2 Account (Reichhaltige Struktur) | Emarsys Corporate Account (Zielstruktur) |
| :--- | :--- | :--- |
| **Vertriebsdaten** | **`salesArrangements`** (z.B. Vertriebsweg, Sparte, Organisationseinheit), **Incoterms**, **Zahlungsbedingungen**. | **Keine Standardfelder** – Sales-relevante Daten müssen in **Custom Fields** abgebildet werden (z.B. `sales_organization` als einfaches Textfeld). |
| **Beziehungen** | **`accountTeamMembers`** (Rollen und Verantwortlichkeiten), **`primaryContactId`**, komplexe Adressstruktur. | Primäre Verknüpfung über die **Corporate Account ID**. **Sales-Verantwortliche** werden oft als **Contact-Attribute** (Personalisierungstoken) importiert. |
| **Identifikatoren** | Interne GUID, **`externalIds`** (für ERP- oder Altsysteme), **`displayId`**. | Interne ID, **External ID** (wird für die SAP Sales Cloud V2 ID benötigt). |
| **KI-Funktionen** | **AI-Assisted Account Synopsis** (z.B. Zusammenfassung von Account-Aktivitäten). | **Loyalty Management** und **Predictive Segmentation** (z.B. Churn-Risiko). |

---

## III. Implikationen für die Datenmigration (SAP Sales Cloud V2 $\rightarrow$ Emarsys)

Die Migration von SAP Sales Cloud V2 nach Emarsys ist ein Prozess der **Datenharmonisierung** und **Filterung**.

### 1. Datenreduktion und Vereinfachung

Ein Großteil der in der SAP Sales Cloud V2 benötigten Sales-Master-Daten ist für Marketing-Automatisierung in Emarsys **nicht direkt relevant** und sollte im Rahmen der Migration **ignoriert oder vereinfacht** werden.

| V2-Feld(gruppe) | Empfohlene Emarsys-Strategie |
| :--- | :--- |
| **`salesArrangements`** | **Ignorieren** oder nur die **wichtigsten** Felder (z.B. `sales_territory`) als Emarsys **Custom Fields** mappen. |
| **Finanzdaten** | **Ignorieren**. Marketing nutzt meist Kaufhistorie (Transaktionsdaten), nicht die Stammdaten der Zahlungsbedingungen. |
| **Interne GUID** | Mapping auf die **External ID** des Emarsys Corporate Account, um eine eindeutige Referenz zu gewährleisten. |

### 2. ID-Management und Synchronisation

Die **eindeutige Identifikation** ist der kritischste Punkt für die bidirektionale Synchronisation:

* **SAP Sales Cloud V2 Account ID** muss als **External ID** im Emarsys Corporate Account gespeichert werden.
* **SAP Sales Cloud V2 Contact ID** muss als **External ID** im Emarsys Contact gespeichert werden.

### 3. Account- und Kontaktbeziehung

Emarsys ist **Contact-centric**. Die Account-Daten der V2 dienen primär dazu, die **Kontakte** im Marketingkontext anzureichern:

* **Synchronisation der Kontakte:** Die im SAP Sales Cloud V2 Account gespeicherten Ansprechpartner (`hasContactPersons`) müssen als individuelle **Kontakte** in Emarsys angelegt werden.
* **Verknüpfung:** Der Emarsys **Corporate Account** wird erstellt. Alle zugehörigen Emarsys-Kontakte werden über das Feld **`Corporate Account ID`** mit diesem Account verknüpft.
* **Verantwortlicher Sales-Mitarbeiter:** Informationen über den **Account Owner** (z.B. Name und E-Mail des Verkäufers) aus dem V2-Objekt sollten als **Personalization Field** zum Emarsys-Kontakt hinzugefügt werden, um **"Send on Behalf of Sales"** (E-Mails im Namen des Verkäufers) zu ermöglichen.