---


---

<h1 id="csc">CSC</h1>
<hr>
<hr>
<h2 id="td4-autorités-de-certification">TD4 Autorités de certification</h2>
<hr>
<h3 id="quest-ce-quune-ca-">Qu’est ce qu’une CA ?</h3>
<h4 id="informations-du-certificat">1. Informations du certificat</h4>
<p>La CA a une paire de clé publique/privée avec lesquels elle va monter son certificat.</p>
<ul>
<li>Les certificats de l’autorité de certification contiennent l’identité de l’autorité de certification, la date de validité du certificat, “identité”, la clé publique de l’autorité de certification. Ce certificat est signé avec la clé privée du CA donc lié à la clé publique qu’il contient.</li>
<li>Les certificats des sites contiennent l’identité du site, hostname (CNAME), la date de validité du certificat, l’identité de l’autorité de certification, clé publique du site. Ce certificat est signé par le CA avec sa clé privée.</li>
</ul>
<h4 id="validité-du-certificat">2. Validité du certificat</h4>
<p>Pour vérifier la validité d’un certificat :</p>
<ul>
<li>Vérifier la signature du CA</li>
<li>vérifier la date de validité</li>
<li>vérifier que le CNAME correspond au hostname de l’url</li>
</ul>
<p>Il est prérequis d’avoir au préalable enregistré le certificat de la CA.</p>
<hr>
<h3 id="organisation-dune-ca-à-étage">Organisation d’une CA à étage</h3>

<table>
<thead>
<tr>
<th align="center">CA</th>
<th align="center">Site</th>
<th align="center">Client</th>
</tr>
</thead>
<tbody>
<tr>
<td align="center">PubCALong / PrivCALong / {CertCALong}PrivCALong</td>
<td align="center">PubSite / PrivSite / {CertSite}PrivCACourte</td>
<td align="center">CertCALong</td>
</tr>
<tr>
<td align="center">PubCACourte / PrivCACourte / {CertCACourte}PrivCALong</td>
<td align="center"></td>
<td align="center"></td>
</tr>
</tbody>
</table><hr>
<h3 id="révocation">Révocation</h3>
<h4 id="crl-certificate-revocation-list">CRL (Certificate Revocation List)</h4>
<p>Blacklist des certificats, doit être consulter régulièrement par les clients, il faut ensuite renvoyer cette liste qui fait plusieurs méga à tout les clients ce qui a un coût très élevé, ce n’est donc pas utilisé.</p>
<h4 id="ocsp-online-certificate-status-protocol">OCSP (Online Certificate Status Protocol)</h4>
<p>Requète sur la base de donnée des certificats révoqués.</p>
<h4 id="agrafage-ocsp">Agrafage OCSP</h4>
<p>Les serveurs https doivent faire retamponner leurs certificats tout les jours par le CA ainsi seuls les certificats tamponné dans les 24 heures sont valides. Cela permet nottement de diminuer la charge au niveau des CA puisqu’au lieu de répondre à chaque clients demandant un certificat ils tamponnent le certificat d’un serveur une seul fois. Cela permet aussi d’éviter de ralentir le client qui devait demander la validité du certificat.</p>
<h4 id="certificats-ev">Certificats EV</h4>
<p>Ces certificats “premium” ont un  service de révocation fonctionnel avec OCSP que la plus part des autres certificats n’ont pas il sont cependant plus compliqués à obtenir car necessitant quelques documents administratifs.</p>
<hr>
<h3 id="compromission">Compromission</h3>
<p>Le mauvais comportement ou l’attaque de l’autorité de certification la plus faible compromet toutes les autres et le système https.</p>
<hr>
<hr>
<h2 id="td5-danepgp">TD5 DANE/PGP</h2>
<hr>
<h3 id="intro-rappel">Intro Rappel</h3>
<p>DNS est une table liant les nom symboliques (de domaines) et les adresses ip des serveurs.<br>
Lorsque l’on veut accéder à un site web (ex <a href="http://www.facebook.com">www.facebook.com</a>) on regarde d’abord le fichier host local, la plupart du temps les sites n’y sont pas, on passe donc par le serveur DNS configuré qui garde ensuite l’adresse du site dans son cache pendant le TTL ce qui lui permet de contacter directement l’adresse plutôt que de relancer une recherche. DNS ne prévois aucun mécanismes de sécurité, c’est pourquoi DNSSEC est apparu.</p>
<hr>
<h3 id="dane">DANE</h3>
<h4 id="principe-simplifié-dnssec">Principe simplifié DNSSEC</h4>
<ul>
<li>ICANN</li>
</ul>
<p>&lt;Kpub, Kpr&gt;</p>

<table>
<thead>
<tr>
<th>.</th>
<th>DNSKey</th>
<th>Kpub</th>
</tr>
</thead>
<tbody>
<tr>
<td>fr</td>
<td>NS</td>
<td><a href="http://ns.fr">ns.fr</a></td>
</tr>
<tr>
<td><a href="http://ns.fr">ns.fr</a></td>
<td>A</td>
<td>@IP</td>
</tr>
<tr>
<td>fr</td>
<td>DS</td>
<td>h(K’pub)</td>
</tr>
<tr>
<td>fr</td>
<td>RRSIG</td>
<td>Sig(h(K’pub))<sub>Kpr</sub></td>
</tr>
</tbody>
</table><ul>
<li>AFNIC</li>
</ul>
<p>&lt;K’pub, K’pr&gt;</p>

<table>
<thead>
<tr>
<th>.fr</th>
<th>DNSKey</th>
<th>K’pub</th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="http://insa-lyon.fr">insa-lyon.fr</a></td>
<td>NS</td>
<td><a href="http://ns.insa-ly.fr">ns.insa-ly.fr</a></td>
</tr>
<tr>
<td><a href="http://ns.insa-ly.fr">ns.insa-ly.fr</a></td>
<td>A</td>
<td>@IP</td>
</tr>
<tr>
<td>insa-lyon</td>
<td>DS</td>
<td>h(K’'pub)</td>
</tr>
<tr>
<td>insa-lyon</td>
<td>RRSIG</td>
<td>Sig(h(K’'pub))<sub>K’pr</sub></td>
</tr>
</tbody>
</table><ul>
<li>INSA</li>
</ul>
<p>&lt;K’‘pub, K’'pr&gt;</p>

<table>
<thead>
<tr>
<th>insa-lyon</th>
<th>DNSKey</th>
<th>K’'pub</th>
</tr>
</thead>
<tbody>
<tr>
<td>insa-lyon</td>
<td>MX</td>
<td>mail.insa-ly</td>
</tr>
<tr>
<td>mail</td>
<td>A</td>
<td>@IPmail</td>
</tr>
<tr>
<td>(E1) www</td>
<td>A</td>
<td>@IP</td>
</tr>
<tr>
<td>www</td>
<td>RRSIG A</td>
<td>Sig(E1)<sub>K’'pr</sub></td>
</tr>
</tbody>
</table><p>Modification ou ajout d’un enregistrement à <a href="http://insa-lyon.fr">insa-lyon.fr</a> :</p>
<p>ex: <a href="http://depart-numerique.insalyon.fr">depart-numerique.insalyon.fr</a></p>
<p>Aucun impact sur les autres zones, il faut seulement ajouter/ modifier l’enregitrement en question et resigner cet enregistrement.</p>
<p>Si changement de clé de insa-lyon, resigner tout les enregitrement et changement de la signature de notre clé publique au niveau de la zone parente.</p>
<hr>
<hr>
<h2 id="cours">Cours</h2>
<div class="mermaid"><svg xmlns="http://www.w3.org/2000/svg" id="mermaid-svg-IxrQhNirYKn9nyB5" height="100%" width="100%" style="max-width:450px;" viewBox="-50 -10 450 266"><g></g><g><line id="actor211" x1="75" y1="5" x2="75" y2="255" class="actor-line" stroke-width="0.5px" stroke="#999"></line><rect x="0" y="0" fill="#eaeaea" stroke="#666" width="150" height="65" rx="3" ry="3" class="actor"></rect><text x="75" y="32.5" dominant-baseline="central" alignment-baseline="central" class="actor" style="text-anchor: middle;"><tspan x="75" dy="0">Client</tspan></text></g><g><line id="actor212" x1="275" y1="5" x2="275" y2="255" class="actor-line" stroke-width="0.5px" stroke="#999"></line><rect x="200" y="0" fill="#eaeaea" stroke="#666" width="150" height="65" rx="3" ry="3" class="actor"></rect><text x="275" y="32.5" dominant-baseline="central" alignment-baseline="central" class="actor" style="text-anchor: middle;"><tspan x="275" dy="0">Server</tspan></text></g><defs><marker id="arrowhead" refX="5" refY="2" markerWidth="6" markerHeight="4" orient="auto"><path d="M 0,0 V 4 L6,2 Z"></path></marker></defs><defs><marker id="crosshead" markerWidth="15" markerHeight="8" orient="auto" refX="16" refY="4"><path fill="black" stroke="#000000" stroke-width="1px" d="M 9,2 V 6 L16,4 Z" style="stroke-dasharray: 0, 0;"></path><path fill="none" stroke="#000000" stroke-width="1px" d="M 0,1 L 6,7 M 6,1 L 0,7" style="stroke-dasharray: 0, 0;"></path></marker></defs><g><text x="175" y="93" class="messageText" style="text-anchor: middle;">ensemble de cypher suite</text><line x1="75" y1="100" x2="275" y2="100" class="messageLine0" stroke-width="2" stroke="black" marker-end="url(#arrowhead)" style="fill: none;"></line></g><g><text x="175" y="128" class="messageText" style="text-anchor: middle;">Cipher cert</text><line x1="275" y1="135" x2="75" y2="135" class="messageLine0" stroke-width="2" stroke="black" marker-end="url(#arrowhead)" style="fill: none;"></line></g><g><text x="175" y="163" class="messageText" style="text-anchor: middle;">Cert</text><line x1="75" y1="170" x2="275" y2="170" class="messageLine0" stroke-width="2" stroke="black" marker-end="url(#arrowhead)" style="fill: none;"></line></g><g><rect x="0" y="190" fill="#eaeaea" stroke="#666" width="150" height="65" rx="3" ry="3" class="actor"></rect><text x="75" y="222.5" dominant-baseline="central" alignment-baseline="central" class="actor" style="text-anchor: middle;"><tspan x="75" dy="0">Client</tspan></text></g><g><rect x="200" y="190" fill="#eaeaea" stroke="#666" width="150" height="65" rx="3" ry="3" class="actor"></rect><text x="275" y="222.5" dominant-baseline="central" alignment-baseline="central" class="actor" style="text-anchor: middle;"><tspan x="275" dy="0">Server</tspan></text></g></svg></div>

