# NodeRed Heishamon controle opties

Heishamon van 'Ygaras' maakt heel veel informatie van de Panasonic warmtepomp beschikbaar via MQTT.
Er zijn meerdere opties om gebruik te maken van deze MQTT informatie.

1: MQTT > Home Assistant

2: MQTT > OpenHab

3: MQTT > Domoticz

4: MQTT > NodeRed

5: ... vast nog wat


Al deze opties kunnen de controle over de WP overnemen in meer/mindere maten. 
Ik heb gekozen voor NodeRed (=NR). Ik gebruik al heel veel NR icm HomeAssistant. In veel gevallen is NR de controller en HomeAssistant (=HA) de Interface. Dit kan ook anders, maar dereden dat ik hier voor gekozen heb is dat bij een herstart van HA, de sturing en bediening van de Pana niet stopt. 

Ik heb dus gekozen voor optie 4 in bovenstaande lijstje. In dezeg GIT kun je dus diverse functies vinden die hier mee te maken hebben.

