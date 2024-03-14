---
layout: page
permalink: /aiplatformtest
title: Tekoäly Platformin Testaus
title_long: AIPlatform
inheader: no
---


<div style="color:black; border-style: solid; border-width: thick; border-color: red; padding: 10px; margin-bottom: 15px; padding: 10px; background-color: #F1EFEF;">

<h4>Vasta Koekäytössä</h4>

<p>
Tekoälyplatformi on tällä kurssilla (4 periodi lukukaudella 23-24) 
vasta koekäytössä. Kurssin henkilökuntakin vasta harjoittelee sen kanssa. Näinpä ohjeissa voi olla jonkin verran epäselvyyksiä ja käytännöt muuttua. Pyrimme minimoimaan muutoksista aiheutuvat häiriöt opiskelijoille. 

Tällä hetkellä käyttöliittymä toimii vain Linuxilla. 
 </p>

</div>

Tällä kurssilla on mahdollista koekäyttää harjoitustyölle kehitteillä olevaa tekoälyjen kehitysalustaa. Tällä hetkellä alusta tukee shakkipelin tekoälyn kehitystä **Linuxilla**. Jos siis aiot tehdä harjoitystyönäsi shakille tekoälyn, suosittelemme tämän käyttöliittymän kokeilemista ja siitä aktiivisen palautteen antoa. 

Lyhyesti sanottuna, alustalta saa käyttöönsä grafisen käyttöliittymän ja mahdollisuuden (melko) helposti pelata omaa tekoälyään vastaan. Tämän tarkoitus on helpottaa harjoitustyön ytimen (eli itse tekoälyn) kehittämiseen pääsemistä. 

**Mikäli aiot kokeilla käyttöliittymää**:
1. Kerro tästä määrittelydokumentissasi. 
1. Lisää jokaiseen viikkoraporttiin lyhyt palaute sen toimivuudesta ja siitä, mikä vaikutus sillä on harjoitustyön kehitykseen. 


## Ohjeet Käyttöliittymän Lataamiseen
![]({{ "/images/ailocal0.png" | absolute_url }})

1. Aloita lataamalla github release [AI-localin](https://github.com/game-ai-platform-team/tira-ai-local/releases/tag/v1.0.0) repositoriosta (kuvan .zip tiedosto).
1. Pura zip tiedosto ja käynnistä sieltä löytyvä tira-ai-local ohjelma (joko terminaalista )

![]({{ "/images/ailocal1.png" | absolute_url }})

Käynnistyvään ohjelmaan voit linkata oman tekoälyprojektisi root kansion (file-path ruutuun jonka jälkeen paina submit). 

## Oman Tekoälyn Konfiguroiminen 

Mallia oman tekoälyn konfiguroimiseen voi ottaa [stupid chess AI](https://github.com/game-ai-platform-team/stupid-chess-ai) projektista. Tiraconfig kansioon laitetaan 
shell scriptit jolla projekti setupataan ja ajetaan. 

Oma tekoäly kirjoitetaan python tiedostoon jota "runcommand" scripti kutsuu (esimerkissä /src/stupid_ai.py)

### Käyttöliittymän Kanssa Keskustelu
Katsotaan vielä esimerkkiprojektin tekoälyn (stupid_ai.py) rakennetta. 
```python
import random
import time
# chess kirjaston käyttö EI OLE SALLITTUA harjoitustyössä
import chess


def main():

    board = chess.Board()

    while True:
        opponent_move = input()
        time.sleep(random.randrange(1,10)/100)
        if opponent_move != "":
            board.push_uci(opponent_move)
        legal_moves = [move.uci() for move in list(board.legal_moves)]
        choice = random.choice(legal_moves)
        board.push_uci(choice)

        # example about logs
        print(f"I moved {choice}\n")
        # example about posting a move
        print(f"MOVE: {choice} \n")                                                                                                              

if __name__ == "__main__":
    main()
```
**Huom** tämä esimerkki käyttää pythonin valmista chess kirjastoa jonka käyttö *ei* ole sallittua harjoitustyössä. Toteuta siis itse laudan hallinta ja siirtojen kirjoitus.

Esimerkissä tekoäly keskustelee käyttöliittymän kanssa input() ja print komentojen kautta. 
Input komenolla luetaan käyttöliitymästä tuleva (ihmisen tekemä) siirto ns. uci muodossa. 
Tämän jälkeen uusi siirto tallennetaan tekoälyn omaan laudan hallintaan (esimerkissä rivillä 66) jonka jälkeen oman tekoäly laskee seuraavan siirron. Esimerkkitekoälymme yksinkertaisesti arpoo sattumanvaraisesti jonkun laillisista siirroista ja palauttaa sen sitten rivillä 74. Muista ettei oma harjoitustyösi saa käyttää chess kirjaston metodeja. 

Käyttöliittymä tukee myös muun syötteen loggaamista. Kaikki print lauseet paitsi ne, jotka alkavat "MOVE:" syötetään käyttöliittymän outputtiin josta ne voi lukea ohjelman aavamassa terminaalissa.
Jos yrität omasta tekoälystäsi lähettää laittoman siirron, käyttöliittymä kaattuu.  

### Siirtojen Käsittely Merkkijonoina
Käyttöliitymä tukee ns uci standardia shakin siirtojen merkitsemiseen merkkijonoina. 
Jokaisessa siirtoa kuvaavassa merkkijonossa on neljä tai viisi merkkiä.  
Merkkijonon kaksi ensimmäistä merkkiä ovat siirrettävän nappulan lähtöruutu. Seuraavat kaksi 
ovat ruutu jonne se siirretään. Katso alla oleva kuva, valkoiset nappulat aloittavar riveiltä 1 ja 2, mustat riveiltä 7 ja 8. Valkoinen kuningas aloittaa ruudusta e1 ja valkoinen kuningatar ruudusta d1. 

Viidettä merkkiä käytetään vain sotilaiden korotuksessa. 
Viides merkki kuvaa nappulaa johon sotilas korotetaan:
**q**->kuningatar, **r**->torni (rook), **b**->lähetti (bishop), **k**->ratsu (knight)

![]({{ "https://ucichessengine.files.wordpress.com/2011/03/square_numbers.gif" | absolute_url }})
 
Esimerkiksi merkkijono "e2e4" kuvastaa nappulan, joka tällä hetkellä on ruudussa e2 siirtoa ruutuun e4.
Merkkijono "e7e8q" kuvastaa sotilaan joka tällä hetkellä on ruudussa e7 korotusta kuningattareksi.  

Tornitus merkataan kuninkaan kahden tai kolmen askeleen siirtona. Mikäli yrität siirtää tornia, tämä tulkitaan tornin siirtona. 

## Kiitos
Kiitokset tekoälyplatformin kehittämisestä kuuluu Ohjelmistotuoantoprojektin Ryhmälle. 
Koko projekti löytyy [Githubista](https://github.com/game-ai-platform-team), tänne sivulle tulee ehkä heidän nimiä kunhan kysyn siihen luvan.



{% include typo_instructions.md %}