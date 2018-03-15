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
<td>Sig(h(K’pub))Kpr</td>
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
<td>Sig(h(K’'pub))K’pr</td>
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
<td>Sig(E1)K’'pr</td>
</tr>
</tbody>
</table><p>Modification ou ajout d’un enregistrement à <a href="http://insa-lyon.fr">insa-lyon.fr</a> :</p>
<p>ex: <a href="http://depart-numerique.insalyon.fr">depart-numerique.insalyon.fr</a></p>
<p>Aucun impact sur les autres zones, il faut seulement ajouter/ modifier l’enregitrement en question et resigner cet enregistrement.</p>
<p>Si changement de clé de insa-lyon, resigner tout les enregitrement et changement de la signature de notre clé publique au niveau de la zone parente.</p>

