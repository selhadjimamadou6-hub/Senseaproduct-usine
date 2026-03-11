import React, { useState } from "react";

// ── Storage ───────────────────────────────────────────────────────────────────
const PROD_KEY   = "usine-production-data";
const RECLAM_KEY = "usine-reclamations-data";

function loadProd()    { try{const r=localStorage.getItem(PROD_KEY);  return r?JSON.parse(r):[];}catch{return[];} }
function saveProd(d)   { try{localStorage.setItem(PROD_KEY,  JSON.stringify(d));}catch(e){console.error(e);} }
function loadReclam()  { try{const r=localStorage.getItem(RECLAM_KEY);return r?JSON.parse(r):[];}catch{return[];} }
function saveReclam(d) { try{localStorage.setItem(RECLAM_KEY,JSON.stringify(d));}catch(e){console.error(e);} }

// ── Constantes ────────────────────────────────────────────────────────────────
const BRANCHES = ["🌍 Poisson Afrique","🇪🇺 Sous-traitant Europe","🇨🇳 Marché Chinois"];
const COLORS = {
  "🌍 Poisson Afrique":      {bg:"#1a3a5c",light:"#e8f0f8",accent:"#2e75b6",text:"#1a3a5c"},
  "🇪🇺 Sous-traitant Europe": {bg:"#2d5016",light:"#e8f5e2",accent:"#70ad47",text:"#2d5016"},
  "🇨🇳 Marché Chinois":       {bg:"#7b0000",light:"#fde8e8",accent:"#c00000",text:"#7b0000"},
};
const TYPES_RECLAM = ["Écailles","Calibrage","Présentation","Emballage","Retard livraison","Autre"];
const STATUTS = {
  ouvert:   {label:"🔴 Ouvert",   bg:"#7f1d1d",color:"#f87171"},
  en_cours: {label:"🟡 En cours", bg:"#78350f",color:"#fcd34d"},
  cloture:  {label:"🟢 Clôturé",  bg:"#14532d",color:"#4ade80"},
};

// ── Helpers ───────────────────────────────────────────────────────────────────
function calcCout(e){
  if(e.branche==="🌍 Poisson Afrique") return ((e.j_debarq||0)+(e.j_prod||0)+(e.j_demoul||0))*4000;
  if(e.branche==="🇪🇺 Sous-traitant Europe") return e.especes.reduce((s,x)=>s+(parseFloat(x.poids)||0),0)*125;
  return null;
}
function getTotalJours(e){
  return e.branche==="🌍 Poisson Afrique"?(e.j_debarq||0)+(e.j_prod||0)+(e.j_demoul||0):(e.j_total||0);
}
function getTotalPoids(e){ return e.especes.reduce((s,x)=>s+(parseFloat(x.poids)||0),0); }
function fmt(n,u=""){ if(n===null||n===undefined)return "—"; return n.toLocaleString("fr-FR",{maximumFractionDigits:1})+(u?" "+u:""); }
function fmtF(n){ if(n===null||n===undefined)return "—"; return n.toLocaleString("fr-FR",{maximumFractionDigits:0})+" F"; }
function todayStr(){ return new Date().toISOString().split("T")[0]; }

// ══════════════════════════════════════════════════════════════════════════════
// FORMULAIRE PRODUCTION (Moustapha)
// ══════════════════════════════════════════════════════════════════════════════
function Formulaire({onSave,todayEntries}){
  const [branche,setBranche]=useState(BRANCHES[0]);
  const [date,setDate]=useState(todayStr());
  const [especes,setEspeces]=useState([{nom:"",poids:""}]);
  const [jd,setJd]=useState(""); const [jp,setJp]=useState(""); const [jm,setJm]=useState("");
  const [jt,setJt]=useState(""); const [obs,setObs]=useState(""); const [saved,setSaved]=useState(false);

  const col=COLORS[branche];
  const totalJ=branche==="🌍 Poisson Afrique"?(parseInt(jd)||0)+(parseInt(jp)||0)+(parseInt(jm)||0):(parseInt(jt)||0);
  const totalP=especes.reduce((s,e)=>s+(parseFloat(e.poids)||0),0);
  const cout=branche==="🌍 Poisson Afrique"?totalJ*4000:branche==="🇪🇺 Sous-traitant Europe"?totalP*125:null;
  const rend=totalJ>0?totalP/totalJ:null;
  const dejaSaisi=todayEntries.filter(e=>e.branche===branche);

  function submit(){
    if(!date)return;
    onSave({id:Date.now(),date,branche,especes,j_debarq:parseInt(jd)||0,j_prod:parseInt(jp)||0,
      j_demoul:parseInt(jm)||0,j_total:parseInt(jt)||0,obs,createdAt:new Date().toISOString()});
    setSaved(true); setTimeout(()=>setSaved(false),2500);
    setEspeces([{nom:"",poids:""}]); setJd(""); setJp(""); setJm(""); setJt(""); setObs("");
  }
  const inp={width:"100%",padding:"10px 12px",borderRadius:8,border:"1.5px solid #ddd",fontFamily:"inherit",fontSize:14,boxSizing:"border-box"};

  return (
    <div style={{background:"#f4f6f9",minHeight:"100vh",padding:16}}>
      <div style={{background:col.bg,borderRadius:16,padding:"20px 24px",marginBottom:20,color:"#fff"}}>
        <div style={{fontSize:12,opacity:.7,marginBottom:4}}>Rapport journalier</div>
        <div style={{fontSize:22,fontWeight:800}}>Bonjour Moustapha 👋</div>
        <div style={{fontSize:12,opacity:.8,marginTop:4}}>{new Date().toLocaleDateString("fr-FR",{weekday:"long",day:"numeric",month:"long"})}</div>
      </div>

      {dejaSaisi.length>0&&(
        <div style={{background:"#fff",borderRadius:12,padding:14,marginBottom:14,border:`2px solid ${col.light}`}}>
          <div style={{fontSize:12,color:"#888",fontWeight:700,marginBottom:6}}>✅ Déjà saisi aujourd'hui</div>
          {dejaSaisi.map(e=>(
            <div key={e.id} style={{fontSize:13,color:col.text,padding:"5px 0",borderBottom:"1px solid #f0f0f0"}}>
              {e.especes.filter(x=>x.nom).map(x=>`${x.nom}: ${x.poids}kg`).join(" · ")} — {getTotalJours(e)} journ. · {fmtF(calcCout(e))}
            </div>
          ))}
        </div>
      )}

      <div style={{background:"#fff",borderRadius:12,padding:14,marginBottom:14}}>
        <div style={{fontSize:13,fontWeight:700,color:"#333",marginBottom:10}}>Branche de production</div>
        {BRANCHES.map(b=>{const c=COLORS[b];return(
          <button key={b} onClick={()=>setBranche(b)} style={{display:"block",width:"100%",marginBottom:8,padding:"12px 14px",borderRadius:10,
            border:`2px solid ${branche===b?c.bg:"#e8e8e8"}`,background:branche===b?c.light:"#fafafa",
            color:branche===b?c.text:"#888",fontFamily:"inherit",fontWeight:branche===b?800:600,fontSize:14,cursor:"pointer",textAlign:"left"}}>{b}</button>
        );})}
      </div>

      <div style={{background:"#fff",borderRadius:12,padding:14,marginBottom:14}}>
        <label style={{fontSize:13,fontWeight:700,color:"#333",display:"block",marginBottom:8}}>Date</label>
        <input type="date" value={date} onChange={e=>setDate(e.target.value)} style={inp}/>
      </div>

      <div style={{background:"#fff",borderRadius:12,padding:14,marginBottom:14}}>
        <div style={{fontSize:13,fontWeight:700,color:"#333",marginBottom:10}}>
          Effectifs journaliers
          {totalJ>0&&<span style={{marginLeft:8,background:col.light,color:col.text,padding:"2px 10px",borderRadius:20,fontSize:12,fontWeight:800}}>Total : {totalJ}</span>}
        </div>
        {branche==="🌍 Poisson Afrique"?(
          <div style={{display:"grid",gridTemplateColumns:"1fr 1fr 1fr",gap:8}}>
            {[["Débarquement",jd,setJd],["Production",jp,setJp],["Démoulage",jm,setJm]].map(([l,v,s])=>(
              <div key={l}><div style={{fontSize:11,color:"#888",marginBottom:4,fontWeight:600}}>{l}</div>
              <input type="number" min="0" value={v} onChange={e=>s(e.target.value)} placeholder="0"
                style={{...inp,fontSize:18,fontWeight:700,textAlign:"center",padding:"10px 4px"}}/></div>
            ))}
          </div>
        ):(
          <input type="number" min="0" value={jt} onChange={e=>setJt(e.target.value)} placeholder="Nombre de journaliers"
            style={{...inp,fontSize:16,fontWeight:700,textAlign:"center"}}/>
        )}
      </div>

      <div style={{background:"#fff",borderRadius:12,padding:14,marginBottom:14}}>
        <div style={{fontSize:13,fontWeight:700,color:"#333",marginBottom:10}}>
          Espèces produites
          {totalP>0&&<span style={{marginLeft:8,background:col.light,color:col.text,padding:"2px 10px",borderRadius:20,fontSize:12,fontWeight:800}}>Total : {fmt(totalP,"kg")}</span>}
        </div>
        {especes.map((esp,i)=>(
          <div key={i} style={{display:"flex",gap:8,marginBottom:8,alignItems:"center"}}>
            <input value={esp.nom} onChange={e=>{const n=[...especes];n[i]={...n[i],nom:e.target.value};setEspeces(n);}} placeholder="Espèce (ex: Sardinelle)"
              style={{flex:2,padding:"10px 12px",borderRadius:8,border:"1.5px solid #ddd",fontFamily:"inherit",fontSize:14}}/>
            <input type="number" min="0" value={esp.poids} onChange={e=>{const n=[...especes];n[i]={...n[i],poids:e.target.value};setEspeces(n);}} placeholder="kg"
              style={{flex:1,padding:"10px 6px",borderRadius:8,border:"1.5px solid #ddd",fontFamily:"inherit",fontSize:14,textAlign:"center"}}/>
            {especes.length>1&&<button onClick={()=>setEspeces(especes.filter((_,idx)=>idx!==i))}
              style={{width:32,height:32,borderRadius:8,border:"none",background:"#fee",color:"#c00",cursor:"pointer",fontWeight:800,fontSize:16}}>×</button>}
          </div>
        ))}
        <button onClick={()=>setEspeces([...especes,{nom:"",poids:""}])}
          style={{marginTop:4,padding:"8px 14px",borderRadius:8,border:`1.5px dashed ${col.accent}`,background:col.light,color:col.text,fontFamily:"inherit",fontWeight:700,fontSize:13,cursor:"pointer"}}>
          + Ajouter une espèce
        </button>
      </div>

      <div style={{background:"#fff",borderRadius:12,padding:14,marginBottom:14}}>
        <label style={{fontSize:13,fontWeight:700,color:"#333",display:"block",marginBottom:8}}>Observations (facultatif)</label>
        <textarea value={obs} onChange={e=>setObs(e.target.value)} rows={3} placeholder="Problèmes qualité, incidents..." style={{...inp,resize:"vertical"}}/>
      </div>

      {(totalJ>0||totalP>0)&&(
        <div style={{background:col.light,borderRadius:12,padding:14,marginBottom:14,border:`2px solid ${col.accent}`}}>
          <div style={{fontSize:13,fontWeight:800,color:col.text,marginBottom:8}}>📋 Récapitulatif</div>
          <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:8}}>
            {[["Journaliers",fmt(totalJ)],["Poids total",fmt(totalP,"kg")],["Coût MO",cout!==null?fmtF(cout):"TBD"],["Rendement",rend!==null?fmt(rend,"kg/j"):"—"]].map(([l,v])=>(
              <div key={l} style={{background:"#fff",borderRadius:8,padding:"8px 10px"}}>
                <div style={{fontSize:11,color:"#888",fontWeight:600}}>{l}</div>
                <div style={{fontSize:16,fontWeight:800,color:col.text}}>{v}</div>
              </div>
            ))}
          </div>
        </div>
      )}

      <button onClick={submit} style={{width:"100%",padding:16,borderRadius:12,border:"none",
        background:saved?"#22c55e":col.bg,color:"#fff",fontFamily:"inherit",fontWeight:800,fontSize:16,cursor:"pointer",transition:"background .3s"}}>
        {saved?"✅ Enregistré !":"📤 Envoyer le rapport"}
      </button>
      <div style={{height:32}}/>
    </div>
  );
}

// ══════════════════════════════════════════════════════════════════════════════
// MODULE RÉCLAMATIONS — Vue Commercial (saisie) + Vue Qualiticien (suivi)
// ══════════════════════════════════════════════════════════════════════════════

// ── Formulaire Commercial ────────────────────────────────────────────────────
function FormulaireCommercial({onSave}){
  const [client,setClient]=useState("");
  const [date,setDate]=useState(todayStr());
  const [branche,setBranche]=useState(BRANCHES[0]);
  const [type,setType]=useState(TYPES_RECLAM[0]);
  const [description,setDesc]=useState("");
  const [saved,setSaved]=useState(false);

  function submit(){
    if(!client||!description)return;
    onSave({id:Date.now(),client,date,branche,type,description,statut:"ouvert",
      action_corrective:"",date_action:"",confirmation_commercial:"",createdAt:new Date().toISOString()});
    setSaved(true); setTimeout(()=>setSaved(false),2500);
    setClient(""); setDesc(""); setType(TYPES_RECLAM[0]);
  }

  const inp={width:"100%",padding:"11px 12px",borderRadius:8,border:"1.5px solid #ddd",
    fontFamily:"inherit",fontSize:14,boxSizing:"border-box"};

  return (
    <div style={{background:"#f4f6f9",minHeight:"100vh",padding:16}}>
      <div style={{background:"#e26b0a",borderRadius:16,padding:"20px 24px",marginBottom:20,color:"#fff"}}>
        <div style={{fontSize:12,opacity:.7,marginBottom:4}}>Module réclamations</div>
        <div style={{fontSize:22,fontWeight:800}}>Retour Client 📣</div>
        <div style={{fontSize:12,opacity:.8,marginTop:4}}>Saisie par le commercial</div>
      </div>

      <div style={{background:"#fff",borderRadius:12,padding:14,marginBottom:14}}>
        <label style={{fontSize:13,fontWeight:700,color:"#333",display:"block",marginBottom:8}}>Client concerné *</label>
        <input value={client} onChange={e=>setClient(e.target.value)} placeholder="Nom du client / acheteur" style={inp}/>
      </div>

      <div style={{background:"#fff",borderRadius:12,padding:14,marginBottom:14}}>
        <label style={{fontSize:13,fontWeight:700,color:"#333",display:"block",marginBottom:8}}>Date de la réclamation</label>
        <input type="date" value={date} onChange={e=>setDate(e.target.value)} style={inp}/>
      </div>

      <div style={{background:"#fff",borderRadius:12,padding:14,marginBottom:14}}>
        <div style={{fontSize:13,fontWeight:700,color:"#333",marginBottom:10}}>Branche concernée</div>
        {BRANCHES.map(b=>{const c=COLORS[b];return(
          <button key={b} onClick={()=>setBranche(b)} style={{display:"block",width:"100%",marginBottom:8,padding:"10px 14px",
            borderRadius:10,border:`2px solid ${branche===b?c.bg:"#e8e8e8"}`,background:branche===b?c.light:"#fafafa",
            color:branche===b?c.text:"#888",fontFamily:"inherit",fontWeight:branche===b?800:600,fontSize:13,cursor:"pointer",textAlign:"left"}}>{b}</button>
        );})}
      </div>

      <div style={{background:"#fff",borderRadius:12,padding:14,marginBottom:14}}>
        <div style={{fontSize:13,fontWeight:700,color:"#333",marginBottom:10}}>Type de réclamation</div>
        <div style={{display:"flex",flexWrap:"wrap",gap:8}}>
          {TYPES_RECLAM.map(t=>(
            <button key={t} onClick={()=>setType(t)} style={{padding:"8px 14px",borderRadius:20,border:"none",
              background:type===t?"#e26b0a":"#f0f0f0",color:type===t?"#fff":"#555",
              fontFamily:"inherit",fontWeight:type===t?800:600,fontSize:13,cursor:"pointer"}}>
              {t}
            </button>
          ))}
        </div>
      </div>

      <div style={{background:"#fff",borderRadius:12,padding:14,marginBottom:14}}>
        <label style={{fontSize:13,fontWeight:700,color:"#333",display:"block",marginBottom:8}}>Description du problème *</label>
        <textarea value={description} onChange={e=>setDesc(e.target.value)} rows={4}
          placeholder="Décrivez précisément la réclamation du client..." style={{...inp,resize:"vertical"}}/>
      </div>

      <button onClick={submit}
        style={{width:"100%",padding:16,borderRadius:12,border:"none",
          background:saved?"#22c55e":"#e26b0a",color:"#fff",fontFamily:"inherit",fontWeight:800,fontSize:16,cursor:"pointer",transition:"background .3s"}}>
        {saved?"✅ Réclamation enregistrée !":"📤 Soumettre la réclamation"}
      </button>
      <div style={{height:32}}/>
    </div>
  );
}

// ── Suivi Qualiticien ─────────────────────────────────────────────────────────
function SuiviReclamations({reclamations,onUpdate}){
  const [filtre,setFiltre]=useState("tous");
  const [selected,setSelected]=useState(null);
  const [action,setAction]=useState("");
  const [dateAction,setDateAction]=useState(todayStr());
  const [confirmComm,setConfirmComm]=useState("");

  const filtres={tous:"Toutes",ouvert:"🔴 Ouvertes",en_cours:"🟡 En cours",cloture:"🟢 Clôturées"};
  const liste=reclamations.filter(r=>filtre==="tous"||r.statut===filtre).sort((a,b)=>new Date(b.createdAt)-new Date(a.createdAt));

  function openDetail(r){
    setSelected(r);
    setAction(r.action_corrective||"");
    setDateAction(r.date_action||todayStr());
    setConfirmComm(r.confirmation_commercial||"");
  }

  function saveDetail(){
    const newStatut=confirmComm?"cloture":action?"en_cours":"ouvert";
    onUpdate({...selected,action_corrective:action,date_action:dateAction,
      confirmation_commercial:confirmComm,statut:newStatut,updatedAt:new Date().toISOString()});
    setSelected(null);
  }

  const counts={ouvert:reclamations.filter(r=>r.statut==="ouvert").length,
    en_cours:reclamations.filter(r=>r.statut==="en_cours").length,
    cloture:reclamations.filter(r=>r.statut==="cloture").length};

  if(selected) return (
    <div style={{background:"#f4f6f9",minHeight:"100vh",padding:16}}>
      <button onClick={()=>setSelected(null)} style={{marginBottom:16,padding:"8px 14px",borderRadius:8,border:"none",
        background:"#fff",color:"#333",fontFamily:"inherit",fontWeight:700,fontSize:13,cursor:"pointer"}}>
        ← Retour à la liste
      </button>

      {/* En-tête réclamation */}
      <div style={{background:STATUTS[selected.statut].bg,borderRadius:16,padding:"16px 20px",marginBottom:16,color:"#fff"}}>
        <div style={{fontSize:11,opacity:.7,marginBottom:4}}>Réclamation #{selected.id.toString().slice(-4)}</div>
        <div style={{fontSize:18,fontWeight:800,marginBottom:6}}>{selected.client}</div>
        <div style={{display:"flex",gap:8,flexWrap:"wrap"}}>
          <span style={{background:"#ffffff22",borderRadius:20,padding:"3px 10px",fontSize:12,fontWeight:700}}>{selected.type}</span>
          <span style={{background:"#ffffff22",borderRadius:20,padding:"3px 10px",fontSize:12}}>{new Date(selected.date).toLocaleDateString("fr-FR")}</span>
          <span style={{background:"#ffffff22",borderRadius:20,padding:"3px 10px",fontSize:12}}>{selected.branche.split(" ")[0]}</span>
        </div>
      </div>

      {/* Description client */}
      <div style={{background:"#fff",borderRadius:12,padding:14,marginBottom:14}}>
        <div style={{fontSize:12,color:"#e26b0a",fontWeight:800,marginBottom:6}}>📣 RÉCLAMATION DU CLIENT</div>
        <div style={{fontSize:14,color:"#333",lineHeight:1.6}}>{selected.description}</div>
      </div>

      {/* Action corrective — Qualiticien */}
      <div style={{background:"#fff",borderRadius:12,padding:14,marginBottom:14}}>
        <div style={{fontSize:12,color:"#2e75b6",fontWeight:800,marginBottom:8}}>🔧 ACTION CORRECTIVE — Qualiticien</div>
        <textarea value={action} onChange={e=>setAction(e.target.value)} rows={4}
          placeholder="Décrivez l'action corrective prise pour résoudre le problème..."
          style={{width:"100%",padding:"10px 12px",borderRadius:8,border:"1.5px solid #ddd",
            fontFamily:"inherit",fontSize:14,resize:"vertical",boxSizing:"border-box",marginBottom:10}}/>
        <label style={{fontSize:12,fontWeight:700,color:"#555",display:"block",marginBottom:6}}>Date de l'action</label>
        <input type="date" value={dateAction} onChange={e=>setDateAction(e.target.value)}
          style={{width:"100%",padding:"10px 12px",borderRadius:8,border:"1.5px solid #ddd",
            fontFamily:"inherit",fontSize:14,boxSizing:"border-box"}}/>
      </div>

      {/* Confirmation commercial */}
      <div style={{background:"#fff",borderRadius:12,padding:14,marginBottom:14}}>
        <div style={{fontSize:12,color:"#14532d",fontWeight:800,marginBottom:8}}>✅ CONFIRMATION COMMERCIAL</div>
        <textarea value={confirmComm} onChange={e=>setConfirmComm(e.target.value)} rows={3}
          placeholder="Le commercial confirme ici que le client est satisfait et que le problème est résolu..."
          style={{width:"100%",padding:"10px 12px",borderRadius:8,border:"1.5px solid #ddd",
            fontFamily:"inherit",fontSize:14,resize:"vertical",boxSizing:"border-box"}}/>
        {confirmComm&&<div style={{marginTop:8,fontSize:12,color:"#4ade80",fontWeight:700}}>
          ✅ Cette réclamation sera marquée Clôturée à la sauvegarde
        </div>}
      </div>

      <button onClick={saveDetail}
        style={{width:"100%",padding:16,borderRadius:12,border:"none",
          background:"#1a3a5c",color:"#fff",fontFamily:"inherit",fontWeight:800,fontSize:16,cursor:"pointer"}}>
        💾 Enregistrer le suivi
      </button>
      <div style={{height:32}}/>
    </div>
  );

  return (
    <div style={{background:"#f4f6f9",minHeight:"100vh",padding:16}}>
      <div style={{background:"#1a3a5c",borderRadius:16,padding:"20px 24px",marginBottom:20,color:"#fff"}}>
        <div style={{fontSize:12,opacity:.7,marginBottom:4}}>Module réclamations</div>
        <div style={{fontSize:22,fontWeight:800}}>Suivi Qualité 🔍</div>
        <div style={{display:"flex",gap:12,marginTop:10}}>
          {Object.entries(STATUTS).map(([k,v])=>(
            <div key={k} style={{textAlign:"center"}}>
              <div style={{fontSize:20,fontWeight:900,color:v.color}}>{counts[k]}</div>
              <div style={{fontSize:10,opacity:.7}}>{k==="ouvert"?"Ouvert":k==="en_cours"?"En cours":"Clôturé"}</div>
            </div>
          ))}
        </div>
      </div>

      {/* Filtres */}
      <div style={{display:"flex",gap:6,marginBottom:14,flexWrap:"wrap"}}>
        {Object.entries(filtres).map(([k,v])=>(
          <button key={k} onClick={()=>setFiltre(k)}
            style={{padding:"6px 14px",borderRadius:20,border:"none",fontFamily:"inherit",fontWeight:700,
              fontSize:12,cursor:"pointer",background:filtre===k?"#1a3a5c":"#e2e8f0",color:filtre===k?"#fff":"#475569"}}>
            {v}
          </button>
        ))}
      </div>

      {/* Liste */}
      {liste.length===0
        ?<div style={{textAlign:"center",padding:40,color:"#888",fontSize:14}}>Aucune réclamation{filtre!=="tous"?" dans ce statut":""}</div>
        :liste.map(r=>{
          const st=STATUTS[r.statut];
          const col=COLORS[r.branche];
          return (
            <div key={r.id} onClick={()=>openDetail(r)}
              style={{background:"#fff",borderRadius:12,padding:14,marginBottom:10,cursor:"pointer",
                borderLeft:`4px solid ${st.color}`,boxShadow:"0 1px 3px #0001"}}>
              <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start",marginBottom:6}}>
                <div style={{fontSize:15,fontWeight:800,color:"#1a3a5c"}}>{r.client}</div>
                <span style={{background:st.bg,color:st.color,borderRadius:20,padding:"3px 10px",fontSize:11,fontWeight:700,whiteSpace:"nowrap"}}>
                  {st.label}
                </span>
              </div>
              <div style={{display:"flex",gap:6,marginBottom:6,flexWrap:"wrap"}}>
                <span style={{background:col?.light,color:col?.text,borderRadius:20,padding:"2px 8px",fontSize:11,fontWeight:700}}>{r.type}</span>
                <span style={{background:"#f0f0f0",color:"#666",borderRadius:20,padding:"2px 8px",fontSize:11}}>{new Date(r.date).toLocaleDateString("fr-FR")}</span>
                <span style={{background:"#f0f0f0",color:"#666",borderRadius:20,padding:"2px 8px",fontSize:11}}>{r.branche.split(" ")[0]}</span>
              </div>
              <div style={{fontSize:13,color:"#555",overflow:"hidden",textOverflow:"ellipsis",whiteSpace:"nowrap"}}>{r.description}</div>
              {r.action_corrective&&(
                <div style={{marginTop:6,fontSize:12,color:"#2e75b6",fontWeight:600}}>
                  🔧 {r.action_corrective.substring(0,60)}{r.action_corrective.length>60?"...":""}
                </div>
              )}
              {r.confirmation_commercial&&(
                <div style={{marginTop:4,fontSize:12,color:"#4ade80",fontWeight:600}}>
                  ✅ Confirmé par le commercial
                </div>
              )}
              <div style={{marginTop:8,fontSize:11,color:"#aaa",textAlign:"right"}}>Appuyer pour voir le détail →</div>
            </div>
          );
        })
      }
      <div style={{height:32}}/>
    </div>
  );
}

// ══════════════════════════════════════════════════════════════════════════════
// TABLEAU DE BORD
// ══════════════════════════════════════════════════════════════════════════════
function Dashboard({entries,reclamations,cdg,setCdg}){
  const [periode,setPeriode]=useState("mois");
  const [jourDate,setJourDate]=useState(todayStr());
  const now=new Date();

  const filtered=entries.filter(e=>{
    const d=new Date(e.date);
    if(periode==="jour") return e.date===jourDate;
    if(periode==="semaine"){const s=new Date(now);s.setDate(now.getDate()-now.getDay()+1);s.setHours(0,0,0,0);return d>=s;}
    if(periode==="mois") return d.getMonth()===now.getMonth()&&d.getFullYear()===now.getFullYear();
    return true;
  });

  function bStats(b){
    const items=filtered.filter(e=>e.branche===b);
    const poids=items.reduce((s,e)=>s+getTotalPoids(e),0);
    const jours=items.reduce((s,e)=>s+getTotalJours(e),0);
    const cout=items.reduce((s,e)=>{const c=calcCout(e);return s+(c||0);},0);
    return {poids,jours,cout,rend:jours>0?poids/jours:null};
  }
  const sA=bStats("🌍 Poisson Afrique"); const sE=bStats("🇪🇺 Sous-traitant Europe"); const sC=bStats("🇨🇳 Marché Chinois");
  const totalP=sA.poids+sE.poids+sC.poids; const totalC=sA.cout+sE.cout;

  const espMap={};
  filtered.forEach(e=>e.especes.forEach(x=>{if(x.nom){if(!espMap[x.nom])espMap[x.nom]={poids:0,branche:e.branche};espMap[x.nom].poids+=parseFloat(x.poids)||0;}}));
  const espList=Object.entries(espMap).sort((a,b)=>b[1].poids-a[1].poids);
  const obsRecentes=filtered.filter(e=>e.obs).slice(-3).reverse();

  // Stats réclamations
  const reclOuvertes=reclamations.filter(r=>r.statut==="ouvert").length;
  const reclEnCours=reclamations.filter(r=>r.statut==="en_cours").length;
  const PERIODES={jour:"Aujourd'hui",semaine:"Semaine",mois:"Mois",tout:"Tout"};

  return (
    <div style={{background:"#0f1923",minHeight:"100vh",color:"#fff",padding:16}}>
      <div style={{marginBottom:20}}>
        <div style={{fontSize:11,color:"#64748b",fontWeight:700,letterSpacing:2,textTransform:"uppercase",marginBottom:4}}>Tableau de bord</div>
        <div style={{fontSize:24,fontWeight:900,color:"#f1f5f9"}}>Production Usine 🐟</div>
        <div style={{fontSize:12,color:"#64748b",marginTop:2}}>{now.toLocaleDateString("fr-FR",{weekday:"long",day:"numeric",month:"long",year:"numeric"})}</div>
      </div>

      {/* Alerte réclamations */}
      {(reclOuvertes+reclEnCours)>0&&(
        <div style={{background:"linear-gradient(135deg,#7f1d1d,#78350f)",borderRadius:12,padding:"12px 16px",marginBottom:14,
          display:"flex",justifyContent:"space-between",alignItems:"center"}}>
          <div>
            <div style={{fontSize:13,fontWeight:800,color:"#fcd34d"}}>⚠️ Réclamations en attente</div>
            <div style={{fontSize:12,color:"#fca5a5",marginTop:2}}>{reclOuvertes} ouvertes · {reclEnCours} en cours</div>
          </div>
          <div style={{fontSize:28,fontWeight:900,color:"#f87171"}}>{reclOuvertes+reclEnCours}</div>
        </div>
      )}

      <div style={{display:"flex",gap:6,marginBottom:16,flexWrap:"wrap"}}>
        {Object.entries(PERIODES).map(([k,v])=>(
          <button key={k} onClick={()=>setPeriode(k)}
            style={{padding:"6px 14px",borderRadius:20,border:"none",fontFamily:"inherit",fontWeight:700,
              fontSize:12,cursor:"pointer",background:periode===k?"#2e75b6":"#1e2d3d",color:"#fff"}}>{v}</button>
        ))}
        {periode==="jour"&&<input type="date" value={jourDate} onChange={e=>setJourDate(e.target.value)}
          style={{padding:"5px 10px",borderRadius:20,border:"1px solid #2e4a6a",background:"#1e2d3d",color:"#fff",fontFamily:"inherit",fontSize:12}}/>}
      </div>

      <div style={{background:"linear-gradient(135deg,#1a3a5c,#0f1923)",border:"1px solid #2e4a6a",borderRadius:16,padding:20,marginBottom:14,textAlign:"center"}}>
        <div style={{fontSize:11,color:"#64748b",fontWeight:700,marginBottom:4}}>POIDS TOTAL PRODUIT</div>
        <div style={{fontSize:44,fontWeight:900,color:"#60a5fa",lineHeight:1}}>{fmt(totalP)}</div>
        <div style={{fontSize:14,color:"#64748b",fontWeight:600}}>kg</div>
        <div style={{marginTop:12,display:"flex",justifyContent:"center",gap:24}}>
          <div style={{textAlign:"center"}}><div style={{fontSize:10,color:"#64748b"}}>COÛT MO CALCULÉ</div><div style={{fontSize:18,fontWeight:800,color:"#a78bfa"}}>{fmtF(totalC)}</div></div>
          <div style={{width:1,background:"#2e4a6a"}}/>
          <div style={{textAlign:"center"}}><div style={{fontSize:10,color:"#64748b"}}>SAISIES</div><div style={{fontSize:18,fontWeight:800,color:"#34d399"}}>{filtered.length}</div></div>
        </div>
      </div>

      {[["🌍 Poisson Afrique",sA,"#1a3a5c","#2e75b6","4 000 F/j"],["🇪🇺 Sous-traitant Europe",sE,"#1a3a15","#70ad47","125 F/kg"],["🇨🇳 Marché Chinois",sC,"#3b0000","#c00000","TBD"]].map(([b,stats,bgD,acc,tarif])=>{
        const key=`cdg_${b}_${periode}`;const cdgVal=cdg[key]||"";
        const ecart=cdgVal&&stats.cout?parseFloat(cdgVal)-stats.cout:null;
        const ecartPct=ecart!==null&&stats.cout>0?ecart/stats.cout*100:null;
        return (
          <div key={b} style={{background:`linear-gradient(135deg,${bgD},#0f1923)`,border:`1px solid ${acc}33`,borderRadius:16,padding:16,marginBottom:12}}>
            <div style={{display:"flex",justifyContent:"space-between",marginBottom:10}}>
              <div><div style={{fontSize:14,fontWeight:800,color:"#f1f5f9"}}>{b}</div><div style={{fontSize:11,color:"#64748b"}}>Tarif : {tarif}</div></div>
              <div style={{background:`${acc}22`,borderRadius:8,padding:"4px 10px",color:acc,fontWeight:800,fontSize:12}}>{fmt(stats.poids,"kg")}</div>
            </div>
            <div style={{display:"grid",gridTemplateColumns:"1fr 1fr 1fr",gap:8,marginBottom:b!=="🇨🇳 Marché Chinois"?10:0}}>
              {[["Journaliers",fmt(stats.jours)],["Rendement",stats.rend!==null?fmt(stats.rend,"kg/j"):"—"],["Coût MO",stats.cout>0?fmtF(stats.cout):"—"]].map(([l,v])=>(
                <div key={l} style={{background:"#ffffff0d",borderRadius:8,padding:"8px 10px"}}>
                  <div style={{fontSize:10,color:"#64748b",fontWeight:600}}>{l}</div>
                  <div style={{fontSize:13,fontWeight:800,color:"#f1f5f9"}}>{v}</div>
                </div>
              ))}
            </div>
            {b!=="🇨🇳 Marché Chinois"&&(
              <div style={{background:"#ffffff08",borderRadius:10,padding:10}}>
                <div style={{fontSize:11,color:"#64748b",fontWeight:700,marginBottom:6}}>Coût Contrôle de Gestion (F)</div>
                <div style={{display:"flex",gap:8,alignItems:"center"}}>
                  <input type="number" value={cdgVal} onChange={e=>setCdg(prev=>({...prev,[key]:e.target.value}))} placeholder="Saisir le chiffre CdG..."
                    style={{flex:1,padding:"8px 10px",borderRadius:8,border:"1px solid #2e4a6a",background:"#1e2d3d",color:"#fff",fontFamily:"inherit",fontSize:13}}/>
                  {ecartPct!==null&&<div style={{padding:"6px 10px",borderRadius:8,fontWeight:800,fontSize:12,whiteSpace:"nowrap",
                    background:Math.abs(ecartPct)<=5?"#14532d":ecartPct>5?"#7f1d1d":"#14532d",
                    color:Math.abs(ecartPct)<=5?"#4ade80":ecartPct>5?"#f87171":"#4ade80"}}>
                    {ecartPct>0?"+":""}{ecartPct.toFixed(1)}%{Math.abs(ecartPct)<=5?" ✅":ecartPct>5?" 🔴":" 🟢"}
                  </div>}
                </div>
              </div>
            )}
          </div>
        );
      })}

      {espList.length>0&&(
        <div style={{background:"#1e2d3d",borderRadius:16,padding:16,marginBottom:12}}>
          <div style={{fontSize:13,fontWeight:800,color:"#f1f5f9",marginBottom:12}}>🐟 Espèces produites</div>
          {espList.map(([nom,data])=>{
            const pct=totalP>0?data.poids/totalP*100:0; const acc=COLORS[data.branche]?.accent||"#2e75b6";
            return(<div key={nom} style={{marginBottom:10}}>
              <div style={{display:"flex",justifyContent:"space-between",marginBottom:4}}>
                <span style={{fontSize:13,fontWeight:700,color:"#f1f5f9"}}>{nom}</span>
                <span style={{fontSize:12,color:"#64748b"}}>{fmt(data.poids,"kg")} · {pct.toFixed(0)}%</span>
              </div>
              <div style={{height:6,background:"#2e4a6a",borderRadius:3,overflow:"hidden"}}>
                <div style={{height:"100%",width:`${pct}%`,background:acc,borderRadius:3}}/>
              </div>
            </div>);
          })}
        </div>
      )}

      {obsRecentes.length>0&&(
        <div style={{background:"#1e2d3d",borderRadius:16,padding:16,marginBottom:12}}>
          <div style={{fontSize:13,fontWeight:800,color:"#f1f5f9",marginBottom:10}}>⚠️ Observations récentes</div>
          {obsRecentes.map(e=>(
            <div key={e.id} style={{padding:"8px 0",borderBottom:"1px solid #2e4a6a"}}>
              <div style={{fontSize:11,color:"#64748b",marginBottom:2}}>{new Date(e.date).toLocaleDateString("fr-FR")} · {e.branche}</div>
              <div style={{fontSize:13,color:"#f1f5f9"}}>{e.obs}</div>
            </div>
          ))}
        </div>
      )}

      <div style={{background:"#1e2d3d",borderRadius:16,padding:16,marginBottom:32}}>
        <div style={{fontSize:13,fontWeight:800,color:"#f1f5f9",marginBottom:10}}>📋 Dernières saisies</div>
        {filtered.length===0?<div style={{fontSize:13,color:"#64748b",textAlign:"center",padding:20}}>Aucune donnée pour cette période</div>
          :[...filtered].reverse().slice(0,8).map(e=>{
            const c=COLORS[e.branche];
            return(<div key={e.id} style={{display:"flex",justifyContent:"space-between",alignItems:"center",padding:"10px 0",borderBottom:"1px solid #2e4a6a"}}>
              <div>
                <div style={{fontSize:12,color:"#64748b"}}>{new Date(e.date).toLocaleDateString("fr-FR")}
                  <span style={{marginLeft:6,padding:"1px 6px",borderRadius:10,background:`${c?.accent}22`,color:c?.accent,fontSize:11,fontWeight:700}}>{e.branche.split(" ")[0]}</span>
                </div>
                <div style={{fontSize:13,fontWeight:700,color:"#f1f5f9",marginTop:2}}>
                  {e.especes.filter(x=>x.nom).map(x=>`${x.nom} ${x.poids}kg`).join(", ")||"—"}
                </div>
              </div>
              <div style={{textAlign:"right"}}>
                <div style={{fontSize:13,fontWeight:800,color:c?.accent||"#60a5fa"}}>{fmt(getTotalPoids(e),"kg")}</div>
                <div style={{fontSize:11,color:"#64748b"}}>{getTotalJours(e)}j · {calcCout(e)!==null?fmtF(calcCout(e)):"—"}</div>
              </div>
            </div>);
          })
        }
      </div>
    </div>
  );
}

// ══════════════════════════════════════════════════════════════════════════════
// APP PRINCIPALE
// ══════════════════════════════════════════════════════════════════════════════
export default function App(){
  const [view,setView]             = useState("accueil");
  const [entries,setEntries]       = useState(()=>loadProd());
  const [reclamations,setReclam]   = useState(()=>loadReclam());
  const [cdg,setCdg]               = useState({});

  function handleSaveProd(entry){const next=[...entries,entry];setEntries(next);saveProd(next);}
  function handleSaveReclam(r){const next=[...reclamations,r];setReclam(next);saveReclam(next);}
  function handleUpdateReclam(r){const next=reclamations.map(x=>x.id===r.id?r:x);setReclam(next);saveReclam(next);}

  const todayEntries=entries.filter(e=>e.date===todayStr());
  const reclOuvertes=reclamations.filter(r=>r.statut==="ouvert").length;
  const reclEnCours=reclamations.filter(r=>r.statut==="en_cours").length;

  const isDark=view==="dashboard";
  const navBg=isDark?"#0f1923":"#f4f6f9";
  const navBorder=isDark?"#2e4a6a":"#e2e8f0";

  const VIEWS=[
    {key:"formulaire",label:"📝 Saisie",    activeBg:"#1a3a5c"},
    {key:"reclamation_comm",label:"📣 Réclamation", activeBg:"#e26b0a"},
    {key:"suivi_qual",label:"🔍 Suivi",   activeBg:"#2d5016"},
    {key:"dashboard",label:"📊 Dashboard",activeBg:"#7030a0"},
  ];

  if(view==="accueil") return (
    <div style={{fontFamily:"'Nunito',sans-serif",minHeight:"100vh",background:"#f4f6f9",
      display:"flex",flexDirection:"column",alignItems:"center",justifyContent:"center",padding:24}}>
      <div style={{fontSize:52,marginBottom:8}}>🐟</div>
      <div style={{fontSize:26,fontWeight:900,color:"#1a3a5c",marginBottom:4,textAlign:"center"}}>Usine de Production</div>
      <div style={{fontSize:14,color:"#64748b",marginBottom:36,textAlign:"center"}}>Gestion quotidienne de la production</div>
      <div style={{display:"flex",flexDirection:"column",gap:12,width:"100%",maxWidth:340}}>
        {[
          {key:"formulaire",   emoji:"📝",label:"Saisie Matinale",    sub:"Moustapha — rapport du jour",bg:"#1a3a5c",badge:todayEntries.length>0?`${todayEntries.length} ✓`:null,badgeBg:"#22c55e"},
          {key:"reclamation_comm",emoji:"📣",label:"Retour Client",  sub:"Commercial — saisir une réclamation",bg:"#e26b0a",badge:null},
          {key:"suivi_qual",   emoji:"🔍",label:"Suivi Réclamations", sub:"Qualiticien — voir & traiter",bg:"#2d5016",badge:(reclOuvertes+reclEnCours)>0?`${reclOuvertes+reclEnCours} en attente`:null,badgeBg:"#f87171"},
          {key:"dashboard",    emoji:"📊",label:"Tableau de Bord",    sub:"Direction — vue globale",bg:"#7030a0",badge:null},
        ].map(({key,emoji,label,sub,bg,badge,badgeBg})=>(
          <button key={key} onClick={()=>setView(key)}
            style={{padding:"18px 20px",borderRadius:14,border:"none",background:bg,color:"#fff",
              fontFamily:"inherit",fontWeight:800,fontSize:15,cursor:"pointer",
              display:"flex",alignItems:"center",gap:12,textAlign:"left"}}>
            <span style={{fontSize:26}}>{emoji}</span>
            <div style={{flex:1}}>
              <div>{label}</div>
              <div style={{fontSize:11,fontWeight:600,opacity:.7,marginTop:2}}>{sub}</div>
            </div>
            {badge&&<span style={{background:badgeBg||"#fff3",borderRadius:20,padding:"2px 10px",fontSize:12,fontWeight:800,whiteSpace:"nowrap"}}>{badge}</span>}
          </button>
        ))}
      </div>
    </div>
  );

  return (
    <div style={{maxWidth:520,margin:"0 auto",fontFamily:"'Nunito',sans-serif"}}>
      <div style={{position:"sticky",top:0,zIndex:100,background:navBg,padding:"8px 12px",
        display:"flex",gap:6,alignItems:"center",borderBottom:`1px solid ${navBorder}`,flexWrap:"wrap"}}>
        <button onClick={()=>setView("accueil")}
          style={{padding:"5px 10px",borderRadius:8,border:"none",background:isDark?"#1e2d3d":"#fff",
            color:isDark?"#94a3b8":"#64748b",fontFamily:"inherit",fontWeight:700,fontSize:11,cursor:"pointer"}}>
          ← Accueil
        </button>
        {VIEWS.map(({key,label,activeBg})=>(
          <button key={key} onClick={()=>setView(key)}
            style={{padding:"5px 12px",borderRadius:8,border:"none",fontFamily:"inherit",fontWeight:700,fontSize:11,cursor:"pointer",
              background:view===key?activeBg:(isDark?"#1e2d3d":"#e2e8f0"),
              color:view===key?"#fff":(isDark?"#94a3b8":"#475569")}}>
            {label}
          </button>
        ))}
      </div>
      {view==="formulaire"         && <Formulaire onSave={handleSaveProd} todayEntries={todayEntries}/>}
      {view==="reclamation_comm"   && <FormulaireCommercial onSave={handleSaveReclam}/>}
      {view==="suivi_qual"         && <SuiviReclamations reclamations={reclamations} onUpdate={handleUpdateReclam}/>}
      {view==="dashboard"          && <Dashboard entries={entries} reclamations={reclamations} cdg={cdg} setCdg={setCdg}/>}
    </div>
  );
}
