# One weak transaction in ECDSA on the Bitcoin blockchain and with the help of Lattice Attack we received a Private Key to BTC coins


<blockquote class="wp-block-quote"><p><strong>What do we know about the lattice attack?</strong></p></blockquote>



<p>To begin with, the&nbsp;&nbsp;<em>elliptic curve digital signature algorithm</em>&nbsp;<code>(ECDSA)</code>&nbsp;&nbsp;is a common digital signature scheme that we see in many of our code reviews.&nbsp;It has some desirable properties, but can also be very fragile to recover the private key with a side-channel attack that reveals less than one bit of the secret nonce.</p>



<blockquote class="wp-block-quote"><p><code>ECDSA</code>&nbsp;is a special form of the digital signature algorithm&nbsp;&nbsp;<code>(DSA)</code>.&nbsp;<code>DSA</code>&nbsp;is a fairly common digital signature scheme, which is defined by three algorithms: key generation, signature, and verification.&nbsp;<em>The key generation algorithm generates private and public keys;&nbsp;</em>&nbsp;<em>the private key is responsible for creating signatures;&nbsp;</em>&nbsp;<em>and the public key is responsible for verifying signatures.&nbsp;</em>&nbsp;The signature algorithm takes a message and a private key as input and generates a signature.&nbsp;The verification algorithm takes a message, a signature, and a public key as input and returns a value of&nbsp;&nbsp;<code>true</code>&nbsp;or&nbsp;&nbsp;<code>false</code>, indicating whether the signature is valid.</p></blockquote>



<p><code>DSA</code>&nbsp;is defined for any mathematical group, and this scheme is safe as long as the discrete logarithm problem is hard for that group.&nbsp;A commonly used group is the integers modulo a prime number p.</p>



<p>Along with this group, we will have a group generator g and some cryptographically secure&nbsp;&nbsp;<em>hash</em>&nbsp;&nbsp;function&nbsp;&nbsp;<code>H</code>.&nbsp;We can assume that&nbsp;&nbsp;<code>p , g</code>&nbsp;and&nbsp;&nbsp;<code>H</code>&nbsp;will be common knowledge.</p>



<p>Key generation works by first randomly selecting a value&nbsp;&nbsp;<code>x</code>&nbsp;from modulo integers&nbsp;&nbsp;<code>p</code>&nbsp;.&nbsp;Then the value is calculated&nbsp;<code>y = g^x mod p</code></p>



<blockquote class="wp-block-quote"><p>The private key of the signature is&nbsp;&nbsp;<code>x</code>&nbsp;, and the public key is&nbsp;&nbsp;<code>y</code>&nbsp;.&nbsp;The signing key must be kept secret, as it is what allows signatures to be made.</p></blockquote>



<p>The signature algorithm creates a signature from the message&nbsp;&nbsp;<code>m</code>&nbsp;and the secret key x .&nbsp;First, a random group element is generated&nbsp;&nbsp;<code>k</code>&nbsp;.&nbsp;This is known as a nonce, which is important when it comes to attacks.</p>



<p>Then the values ​​are calculated&nbsp;&nbsp;<code>r = g^k mod p</code>&nbsp;and&nbsp;<code>s = ( k^-1 ( H ( m ) + xr )) mod p</code></p>



<p>Here&nbsp;&nbsp;<code>k^- 1</code>&nbsp;, is the inverse group, and&nbsp;&nbsp;<code>H ( m )</code>&nbsp;is the result of computing the hash m, and interpreting the result as an integer modulo p .</p>



<p>The signature is defined as a pair of&nbsp;&nbsp;<code>( r , s )</code>.&nbsp;(Note: if one of the&nbsp;&nbsp;<code>r</code>&nbsp;or&nbsp; values&nbsp;<code>s</code>​​is equal to&nbsp;&nbsp;<code>0</code>, the algorithm restarts with the new value of&nbsp;&nbsp;<code>k</code>&nbsp;).</p>



<p>The verification algorithm receives a signature&nbsp;&nbsp;<code>( r , s )</code>, a message&nbsp;&nbsp;<code>m</code>&nbsp;and a public key y as input.&nbsp;Let&nbsp;&nbsp;<code>ŝ = s^-1</code>&nbsp;, then the algorithm returns true if and only if&nbsp;&nbsp;<code>r , s ≠ 0 и r = ( g H ( m ) y r ) ŝ</code>&nbsp;.</p>



<p>This validation check works because&nbsp;&nbsp;<code>g^H( m ) y^r = g^H(m)+ xr = g^ks</code>, and therefore&nbsp;<code>(g^H(m)y^r)^ŝ = g^k = r</code></p>



<blockquote class="wp-block-quote"><p><em>A digital signature scheme is considered secure if it cannot be counterfeited.</em></p></blockquote>



<p>Immutability has a formal cryptographic meaning, but at a high level it means that you cannot create signatures without knowing the secret key (unless you have copied an already existing signature created from the secret key).&nbsp;Proven to be&nbsp;&nbsp;<code>DSA</code>impossible to fake under the assumption of a discrete log.</p>



<p>The digital signature is defined over the mathematical group.&nbsp;When&nbsp;&nbsp;<code>DSA</code>&nbsp;used with the group of elliptic curves as this mathematical group, we call it&nbsp;&nbsp;<code>ECDSA</code>.&nbsp;<em>The elliptic curve group</em>&nbsp;&nbsp;consists of elliptic curve points, which are pairs of&nbsp;&nbsp;<code>( x , y )</code>, which satisfy the equation&nbsp;&nbsp;<code>y^2 = x^3 + ax + b</code>&nbsp;for some&nbsp;&nbsp;<code>a , b</code>&nbsp;.&nbsp;For this blog post, all you need to know is that using elliptic curves you can define a finite group, which means you get a group generator,&nbsp;&nbsp;<code>g</code>&nbsp;<em>(elliptic curve point)</em>&nbsp;, and&nbsp;&nbsp;<em><u>addition</u></em>&nbsp;&nbsp;and&nbsp;&nbsp;<em><u>dot multiplication operations</u></em>&nbsp;&nbsp;exactly like this the same as you can with integers.&nbsp;Since they form a finite group, generator,<code>g</code>&nbsp;, will have a finite order,&nbsp;&nbsp;<code>p</code>.&nbsp;This blog post will not explain or require you to know how these&nbsp;&nbsp;<em>elliptic curve</em>&nbsp;operations work .</p>



<p><em>The secret key</em>&nbsp;<code>x</code>&nbsp;&nbsp;will still be a random value modulo integers&nbsp;&nbsp;<code>p</code>&nbsp;.&nbsp;<code>ECDSA</code>&nbsp;works the same as&nbsp;&nbsp;<code>DSA</code>, but with a different group.&nbsp;Now the public key&nbsp;&nbsp;<code>y</code>&nbsp;is still computed as&nbsp;&nbsp;<code>y = g^x</code>&nbsp;, except that g is now a point on the elliptic curve.&nbsp;This means that y will also be a point on the elliptic curve (previously y was an integer modulo p ).&nbsp;Another difference is how we calculate the value of r .&nbsp;We still generate a random nonce k as an integer modulo p as before.&nbsp;We will calculate&nbsp;&nbsp;<code>g^k</code>&nbsp;, but again,&nbsp;<code>g</code>&nbsp;is a point of an elliptic curve, and, therefore,&nbsp;&nbsp;<code>g^k</code>&nbsp;also.&nbsp;Therefore, we can calculate&nbsp;&nbsp;<code>( x^k , y^k ) = g^k</code>&nbsp;and set&nbsp;&nbsp;<code>r = x^k</code>&nbsp;.&nbsp;Now the meaning&nbsp;<code>s</code>&nbsp;can be computed as before, and we get our signature&nbsp;&nbsp;<code>( r , s )</code>, which is still an integer modulo&nbsp;&nbsp;<code>p</code>&nbsp;, as before.&nbsp;To check, we need to correct for the fact that we computed&nbsp;&nbsp;<code>r</code>&nbsp;a little differently.</p>



<blockquote class="wp-block-quote"><p>So, as before, we&nbsp;&nbsp;<em>compute</em>&nbsp;&nbsp;the value of&nbsp;&nbsp;<code>( g^H(m)y^r)^ŝ</code>&nbsp;, but now that value is a point on the elliptic curve, so we take&nbsp;&nbsp;<code>x</code>the -coordinate of that point and compare it to our value of&nbsp;&nbsp;<code>r</code>&nbsp;.</p></blockquote>



<p><em>Recovery&nbsp; of&nbsp;<u>secret keys</u>&nbsp;&nbsp;from reused nonces&nbsp;</em><code>NONCES</code></p>



<p>Now that we understand what it is&nbsp;&nbsp;<code>ECDSA</code>&nbsp;and how it works, let’s demonstrate its&nbsp;&nbsp;<em><u>fragility</u></em>&nbsp;.&nbsp;Again, since this is a digital signature scheme, it is imperative that the&nbsp;&nbsp;<em>secret key</em>&nbsp;&nbsp;is never revealed to anyone other than the person signing the message.</p>



<blockquote class="wp-block-quote"><p>However, if the signer ever issues the signature and also issues the used nonce, the&nbsp;&nbsp;<em>attacker</em>&nbsp;&nbsp;can immediately recover the&nbsp;&nbsp;<em><u>secret key</u></em>&nbsp;.</p></blockquote>



<p>Let’s say I release a signature&nbsp;&nbsp;<code>( r , s )</code>&nbsp;for a message&nbsp;&nbsp;<code>m</code>&nbsp;and accidentally discover that I’ve used a nonce&nbsp;&nbsp;<code>k</code>&nbsp;.</p>



<p>Since&nbsp;&nbsp;<code>s = ( k^-1 ( H ( m ) + xr )),</code>&nbsp;we can easily calculate the secret key:</p>



<p><code>s = (k^-1(H(m) + xr))</code></p>



<p><code>ks = H(m) + xr</code></p>



<p><code>ks – H(m) = xr</code></p>



<p><code>x = r^-1(ks – H(m))</code></p>



<blockquote class="wp-block-quote"><p>Therefore, the signer must not only keep their&nbsp;&nbsp;<em><u>private key secret</u></em>&nbsp;, but all of their nonces they have ever generated are secret.</p></blockquote>



<blockquote class="wp-block-quote"><p>Even if the signer keeps each&nbsp;&nbsp;<em><u>nonce&nbsp;</u></em><code>NONCES</code>&nbsp;secret , if he accidentally repeats one&nbsp;&nbsp;<em><u>nonce</u></em>&nbsp;<code>NONCES</code>&nbsp;&nbsp;(even for different messages),&nbsp;&nbsp;<em><u>the secret key</u></em>&nbsp;&nbsp;can also be&nbsp;&nbsp;<strong>recovered</strong>&nbsp;immediately .</p></blockquote>



<p>Let&nbsp;&nbsp;<code>( r , s 1 )</code>&nbsp;and&nbsp;&nbsp;<code>( r , s 2 )</code>&nbsp;be two signatures created on messages&nbsp;&nbsp;<code>m 1</code>&nbsp;and&nbsp;&nbsp;<code>m 2</code>&nbsp;(respectively) from the same nonce&nbsp;&nbsp;<code>k</code>&nbsp;— since they have the same nonce, the r values ​​will be the same, so this is very easy for an attacker to detect:</p>



<p><code>s1 = k^-1(H(m1) + xr) and s2 = k^-1(H(m2) + xr)</code></p>



<p><code>s1 – s2 = k^-1(H(m1) – H(m2))</code></p>



<p><code>k(s1 – s2) = H(m1) – H(m2)</code></p>



<p><code>k = (s1 – s2)^-1(H(m1) – H(m2))</code></p>



<p>Once we have recovered the nonce&nbsp;&nbsp;<code>k</code>&nbsp;using the above formula, we can recover the private key by performing the previously described attack.</p>



<h2>Let’s digest this for a moment.</h2>



<blockquote class="wp-block-quote"><p>If&nbsp; the signing&nbsp;<em><u>nonce&nbsp;</u></em><code>NONCES</code>&nbsp;&nbsp;is ever disclosed, the&nbsp;&nbsp;<em>secret key</em>&nbsp;can be immediately&nbsp;&nbsp;<em><u>recovered</u></em>&nbsp;, which&nbsp;&nbsp;<strong><u>breaks our entire signature scheme</u></strong>&nbsp;.</p></blockquote>



<p>Also, if two nonces ever repeat, no matter what the messages are,&nbsp;&nbsp;<em>an attacker</em>&nbsp;&nbsp;can easily detect this and immediately&nbsp;&nbsp;<strong>recover the secret key</strong>&nbsp;, again breaking our whole scheme.</p>



<p>It’s pretty fragile and it’s only&nbsp;&nbsp;<em>light attacks</em>&nbsp;!</p>



<p>But there is a&nbsp;&nbsp;<em>new&nbsp;</em>&nbsp;<strong><a href="https://youtu.be/YP4Xj6gUcf4" target="_blank" rel="noreferrer noopener">Lattice At&nbsp;</a></strong><a href="https://youtu.be/YP4Xj6gUcf4"><strong>tack</strong></a>&nbsp;&nbsp;which has been described in great detail&nbsp;&nbsp;<a href="https://youtu.be/YP4Xj6gUcf4" target="_blank" rel="noreferrer noopener"><em>(Joachim Breitner and Nadia Heninger)</em></a><code>Йоахим Брайтнер и Надя Хенингер</code></p>



<blockquote class="wp-block-quote"><p>Document&nbsp;&nbsp;<a href="https://eprint.iacr.org/2019/023.pdf" target="_blank" rel="noreferrer noopener">[PDF]</a>&nbsp;:&nbsp;&nbsp;<em>Biased Nonce Sense: Lattice Attacks against Weak ECDSA Signatures in Cryptocurrencies</em></p></blockquote>



<h2>In the Bitcoin blockchain, we found a certain transaction:</h2>



<p>transaction:&nbsp;&nbsp;<a href="https://www.blockchain.com/btc/tx/08d917f0fee48b0d765006fa52d62dd3d704563200f2817046973e3bf6d11f1f" target="_blank" rel="noreferrer noopener">08d917f0fee48b0d765006fa52d62dd3d704563200f2817046973e3bf6d11f1f</a></p>



<p>for Bitcoin Addresses:&nbsp;&nbsp;<a href="https://www.blockchain.com/btc/address/15N1KY5ohztgCXtEe13BbGRk85x2FPgW8E" target="_blank" rel="noreferrer noopener">15N1KY5ohztgCXtEe13BbGRk85x2FPgW8E</a></p>



<blockquote class="wp-block-quote"><p>and we managed to multiply the fake signatures and apply the lattice</p></blockquote>



<p>where using the&nbsp;&nbsp;<em>Python script&nbsp;</em>&nbsp;<a href="https://youtu.be/YP4Xj6gUcf4" target="_blank" rel="noreferrer noopener">algorithmLLL.py</a>&nbsp;&nbsp;with the installation of packages in&nbsp;&nbsp;<strong>GOOGLE COLAB</strong></p>



<p><strong>INSTALL &gt;&gt; SAGE + ECDSA + BITCOIN + algorithm LLL</strong></p>



<blockquote class="wp-block-quote"><p>We managed to get&nbsp;&nbsp;<code>Private Key</code>&nbsp;to&nbsp;&nbsp;<code>Bitcoin Wallet</code>&nbsp;from one weak transaction in&nbsp;&nbsp;<code>ECDSA</code>.</p></blockquote>



<figure class="wp-block-image"><img src="./With the help of Lattice Attack, we received a Private Key to BITCOIN_files/77737e84bb453449fd6956a39c4eb195.png" alt="Installation" title="Installation"><figcaption>Installation</figcaption></figure>



<figure class="wp-block-image"><img src="./With the help of Lattice Attack, we received a Private Key to BITCOIN_files/ae20b37475bfff1ce38f81cc206a93f3.png" alt="Run Bash script: lattice.sh" title="Run Bash script: lattice.sh"><figcaption>Run Bash script: lattice.sh</figcaption></figure>



<figure class="wp-block-image"><img src="./With the help of Lattice Attack, we received a Private Key to BITCOIN_files/72289e9dab54d5aa568438a94a4c90a6.png" alt="Result in HEX format Private key found!" title="Result in HEX format Private key found!"><figcaption>Result in HEX format Private key found!</figcaption></figure>



<figure class="wp-block-image"><img src="./With the help of Lattice Attack, we received a Private Key to BITCOIN_files/46921784bb73f1218ded9e16bc0f8abd.png" alt="File: ONESIGN.txt (ECDSA Signature R, S, Z Value)" title="File: ONESIGN.txt (ECDSA Signature R, S, Z Value)"><figcaption>File: ONESIGN.txt (ECDSA Signature R, S, Z Value)</figcaption></figure>



<figure class="wp-block-image"><img src="./With the help of Lattice Attack, we received a Private Key to BITCOIN_files/8cbe22f9cd364064a8e005ac8ea4e99e.png" alt="We propagated fake signatures for the Python script algorithmLLL.py" title="We propagated fake signatures for the Python script algorithmLLL.py"><figcaption>We propagated fake signatures for the Python script algorithmLLL.py</figcaption></figure>



<figure class="wp-block-image"><img src="./With the help of Lattice Attack, we received a Private Key to BITCOIN_files/83f750d81ba83309039189495a10680a.png" alt="File: PRIVATEKEY.txt" title="File: PRIVATEKEY.txt"><figcaption>File: PRIVATEKEY.txt</figcaption></figure>



<figure class="wp-block-image"><img src="./With the help of Lattice Attack, we received a Private Key to BITCOIN_files/2029821051232d8a95744863eaa65049.png" alt="File: ADDRESS.txt" title="File: ADDRESS.txt"><figcaption>File: ADDRESS.txt</figcaption></figure>



<figure class="wp-block-image"><img src="./With the help of Lattice Attack, we received a Private Key to BITCOIN_files/4ce93fdd168a7acc734929d342c8949c.png" alt="Checking the private key on the bitaddress website" title="Checking the private key on the bitaddress website"><figcaption>Checking the private key on the bitaddress website</figcaption></figure>



<p><em><u>Private key found!</u></em></p>



<figure class="wp-block-embed"><div class="wp-block-embed__wrapper">
https://www.blockchain.com/btc/address/15N1KY5ohztgCXtEe13BbGRk85x2FPgW8E
</div></figure>



<figure class="wp-block-image"><img src="./With the help of Lattice Attack, we received a Private Key to BITCOIN_files/00cbe70723846c402c4da47bcb6d47b3.png" alt="0.001 BTC" title="0.001 BTC"><figcaption>0.001 BTC</figcaption></figure>



<pre class="wp-block-code"><code>ADDR: 15N1KY5ohztgCXtEe13BbGRk85x2FPgW8E
WIF:  5JCAmNLXeSwi2SCgNH7wRL5qSQhPa7sZvj8eDwxisY5hJm8Uh92
HEX:  31AFD65CAD430D276E3360B1C762808D1D051154724B6FC15ED978FA9D06B1C1 </code></pre>



<p>This video was created for the&nbsp;&nbsp;<a href="https://cryptodeep.ru/lattice-attack" target="_blank" rel="noreferrer noopener"><strong>CRYPTO DEEP TECH</strong></a>&nbsp;portal &nbsp;to ensure the financial security of data and secp256k1 elliptic curve cryptography against weak ECDSA signatures in the BITCOIN cryptocurrency</p>



<p><strong><a href="https://youtu.be/YP4Xj6gUcf4" target="_blank" rel="noreferrer noopener">https://youtu.be/YP4Xj6gUcf4</a></strong>&nbsp;—<strong>&nbsp;Video footage</strong></p>



<p><strong>cryptodeeptech@gmail.com — Email for all questions</strong></p>



<p><strong><a href="https://t.me/cryptodeep_tech" target="_blank" rel="noreferrer noopener">https://t.me/cryptodeep_tech</a>&nbsp;— Technical support via Telegram</strong></p>



<p><a href="https://cryptodeeptech.ru/lattice-attack"><strong>https://cryptodeeptech.ru/lattice-attack — Source</strong></a></p>
	</div><!-- .entry-content -->

---


|  | Donation Address |
| --- | --- |
| ♥ __BTC__ | 1Lw2kh9WzCActXSGHxyypGLkqQZfxDpw8v |
| ♥ __ETH__ | 0xaBd66CF90898517573f19184b3297d651f7b90bf |
