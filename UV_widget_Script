/************************************************
 * UV-Widget – Kinderdagverblijf De Drie Biggetjes
 * Stan Wissink (stan@dedriebiggetjeshertme.nl)
 * V1.0
 * Data: Open-Meteo (KNMI-model)
 * Advies gebaseerd op: max UV komende uur
 ************************************************/

// ===== INSTELLINGEN =====
const LATITUDE = 52.313713;     // Vaste locatie
const LONGITUDE = 6.779707;
const INSMEER_DREMPEL = 3;
const EXTRA_ALERT_DREMPEL = 6;

// ===== API =====
const url =
  `https://api.open-meteo.com/v1/forecast` +
  `?latitude=${LATITUDE}` +
  `&longitude=${LONGITUDE}` +
  `&hourly=uv_index` +
  `&daily=uv_index_max` +
  `&timezone=Europe/Amsterdam`;

// ===== DATA OPHALEN =====
let uvKomendUur = null;
let uvMaxVandaag = null;

try {
  const req = new Request(url);
  const data = await req.loadJSON();

  const nu = new Date();
  const tijden = data.hourly.time;
  const uvUren = data.hourly.uv_index;

  // Zoek hoogste UV in het komende uur
  let waardenKomendUur = [];

  for (let i = 0; i < tijden.length; i++) {
    const tijd = new Date(tijden[i]);
    const verschil = (tijd - nu) / (1000 * 60); // minuten

    if (verschil >= 0 && verschil <= 60) {
      waardenKomendUur.push(uvUren[i]);
    }
  }

  uvKomendUur = waardenKomendUur.length > 0
    ? Math.max(...waardenKomendUur)
    : null;

  uvMaxVandaag = data.daily.uv_index_max[0];

} catch (e) {
  uvKomendUur = null;
  uvMaxVandaag = null;
}

// ===== WIDGET =====
const widget = new ListWidget();
widget.setPadding(12, 12, 12, 12);
widget.url =
  "https://kdvnet-filepicker-copy.s3.eu-central-1.amazonaws.com/u0AxwcrRhoy4mVJpiYQE_Protocol%2520Preventie%2520zonnebrand%2520%252B%2520hitteprotocol.pdf";

// ===== FOUTAFHANDELING =====
if (uvKomendUur === null || uvMaxVandaag === null) {
  widget.backgroundColor = new Color("#6B7280"); // grijs

  const titel = widget.addText("☁️ UV niet beschikbaar");
  titel.font = Font.boldSystemFont(16);
  titel.textColor = Color.white();

  widget.addSpacer(6);

  const advies = widget.addText(
    "Volg standaard zonneprotocol\n(insmeren bij zonnig / warm weer)"
  );
  advies.font = Font.systemFont(13);
  advies.textColor = Color.white();

  Script.setWidget(widget);
  Script.complete();
  return;
}

// ===== KLEUR + HANDELINGSADVIES =====
let achtergrond;
let adviesTekst;

if (uvKomendUur < INSMEER_DREMPEL) {
  achtergrond = new Color("#10B981"); // groen
  adviesTekst = "✅ Geen extra zonbescherming nodig";
} else if (uvKomendUur < EXTRA_ALERT_DREMPEL) {
  achtergrond = new Color("#F59E0B"); // oranje
  adviesTekst = "➡️ Insmeren";
} else {
  achtergrond = new Color("#DC2626"); // rood
  adviesTekst = "➡️ Insmeren + schaduw zoeken\nBeperkt buiten spelen";
}

widget.backgroundColor = achtergrond;

// ===== TEKSTCONTAINER (LEESBAARHEID) =====
const textContainer = widget.addStack();
textContainer.layoutVertically();
textContainer.setPadding(8, 8, 8, 8);
textContainer.backgroundColor = new Color("#000000", 0.12); // 12% zwart
textContainer.cornerRadius = 10;

// Titel
const titel = textContainer.addText("☀️ UV index (komend uur)");
titel.font = Font.boldSystemFont(22);
titel.textColor = Color.white();
textContainer.addSpacer(6);

// UV-waarde
const uvNu = textContainer.addText(`${uvKomendUur.toFixed(1)}`);
uvNu.font = Font.boldSystemFont(56);
uvNu.textColor = Color.white();

textContainer.addSpacer(6);

// Handelingsadvies
const advies = textContainer.addText(adviesTekst);
advies.font = Font.boldSystemFont(22);
advies.textColor = Color.white();

// ===== ONDERAAN: MAX VAN DE DAG =====
widget.addSpacer();

const uvDag = widget.addText(`Max UV vandaag: ${uvMaxVandaag.toFixed(1)}`);
uvDag.font = Font.boldSystemFont(16);
uvDag.textColor = new Color("#F9FAFB");

// ===== AFRONDEN =====
Script.setWidget(widget);
Script.complete();
