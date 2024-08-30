# Gene report / MoG+ 

## Parameters

* `hgnc_id` HGNC Id
  * default: 404

## Endpoint

{{SPARQLIST_TOGOVAR_SPARQL}}

## `togovar2mogplus`

```sparql
DEFINE sql:select-option "order"

PREFIX dct:  <http://purl.org/dc/terms/>
PREFIX faldo: <http://biohackathon.org/resource/faldo#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX tgv: <http://togovar.org/vocabulary/>

SELECT DISTINCT ?togovar ?tgv_id ?hgnc_id ?consequence ?mogplus ?mouse_strain ?chr_mm38 ?pos_mm38
WHERE {
  GRAPH <http://togovar.org/mogplus>{
    OPTIONAL{ ?togovar dct:identifier ?tgv_id. }
    ?togovar tgv:hgnc_id ?hgnc_id;
      tgv:most_severe_consequence ?consequence;
      tgv:orthologous_variants ?mogplus.
    ?mogplus tgv:strain ?mouse_strain;
      faldo:reference ?chr_mm38;
      faldo:position ?pos_mm38.
  }
}
```

## `result`

```javascript
({togovar2mogplus}) => {
  const span_variant_region = 50;
  return togovar2mogplus.results.bindings.map(x => {
    const chr_mm38 = x.chr_mm38?.value?.match(/http:\/\/identifires.org\/mco\/([^/]+)/);
    const uri_mm39 = x.mogplus?.value?.match(/http:\/\/identifiers.org\/mco\/(.+)\/GRCm39#(\d+)-(\S+)-(\S+)/);
    const mogplus_mm39 = uri_mm39[1] + ":" + uri_mm39[2] + "-" + uri_mm39[3] + "-" + uri_mm39[4];
    const mmu_chr = chr_mm38[1];
    const mmu_pos = x.pos_mm38.value;

    const mogplus_detail="https://molossinus.brc.riken.jp/mogplus/cgi-bin/adetail.cgi?TPRGRM=alignView&SRCPAGE=alignView&OUTFASTA=0&ACHRVER=mm10&CHRMID=" + mmu_chr + "&CHRMSTRT=" + (parseInt(mmu_pos) - 50) + "&CHRMEND=" + (parseInt(mmu_pos) + 50) + "&AlignRgnFontSizePrcnt=100&AlignRgnDivHeight=800&ISSHOWNCBIMRNA=1&ISSHOWAA=2&ISSHOWDBSNP=1&PresentReadType=2&SHOWMSMV4_SQMM10=c&SHOWJF1V3MM10=c&SHOWKJRV1MM10=c&SHOWSWNV1MM10=c&SHOWCHDV1MM10=c&SHOWNJLV1MM10=c&SHOWBLG2V1MM10=c&SHOWHMIV1MM10=c&SHOWBFMV1MM10=c&SHOWPGN2V1MM10=c&CLNMAPID=0&DRCT=0&DRFT=0";
//    const start = mmu_pos - span_variant_region;
//    const end = mmu_pos + span_variant_region;
//    const mogplus_table="https://molossinus.brc.riken.jp/mogplus/variantTable/?strainNoSlct=refGenome&strainNoSlct=msmv4_sq&strainNoSlct=jf1v3&strainNoSlct=kjrv1&strainNoSlct=swnv1&strainNoSlct=chdv1&strainNoSlct=njlv1&strainNoSlct=blg2v1&strainNoSlct=hmiv1&strainNoSlct=bfmv1&strainNoSlct=pgn2v1&strainNoSlct=129P2_OlaHsd&strainNoSlct=129S1_SvImJ&strainNoSlct=129S5SvEvBrd&strainNoSlct=A_J&strainNoSlct=AKR_J&strainNoSlct=BALB_cJ&strainNoSlct=BTBR_T%2B_Itpr3tf_J&strainNoSlct=BUB_BnJ&strainNoSlct=C3H_HeH&strainNoSlct=C3H_HeJ&strainNoSlct=C57BL_10J&strainNoSlct=C57BL_6NJ&strainNoSlct=C57BR_cdJ&strainNoSlct=C57L_J&strainNoSlct=C58_J&strainNoSlct=CAST_EiJ&strainNoSlct=CBA_J&strainNoSlct=DBA_1J&strainNoSlct=DBA_2J&strainNoSlct=FVB_NJ&strainNoSlct=I_LnJ&strainNoSlct=KK_HiJ&strainNoSlct=LEWES_EiJ&strainNoSlct=LP_J&strainNoSlct=MOLF_EiJ&strainNoSlct=NOD_ShiLtJ&strainNoSlct=NZB_B1NJ&strainNoSlct=NZO_HlLtJ&strainNoSlct=NZW_LacJ&strainNoSlct=PWK_PhJ&strainNoSlct=RF_J&strainNoSlct=SEA_GnJ&strainNoSlct=SPRET_EiJ&strainNoSlct=ST_bJ&strainNoSlct=WSB_EiJ&strainNoSlct=ZALENDE_EiJ&seqType=genome&chrName=" + mmu_chr + "&geneNameSearchText=&index=submit&presentType=disp&chrStart=" + start + "&chrEnd=" + end;
    return {
      tgv_id: x.tgv_id?.value,
      tgv_link: "/variant/" + x.tgv_id?.value,
      consequence: x.consequence?.value,
      mogplus: mogplus_mm39,
      mogplus_detail: mogplus_detail,
      mouse_strain: x.mouse_strain?.value
    };
  });
}
```
