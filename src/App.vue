<script setup>
import {computed, ref, onMounted,onUnmounted,watch,nextTick} from 'vue';
import AccessibilityMenu from './components/AccessibilityMenu.vue';
import {siteConfig as c} from './config/siteConfig';
const focusMain=()=>document.querySelector('main')?.focus();
const route=ref(location.hash.slice(1)||'/');
const update=()=>{route.value=location.hash.slice(1)||'/';window.scrollTo(0,0)};
onMounted(()=>window.addEventListener('hashchange',update));onUnmounted(()=>window.removeEventListener('hashchange',update));
const course=computed(()=>c.courses.find(x=>route.value==='/curso/'+x.id));
const page=computed(()=>route.value.replace('/',''));
const nav=[['duvidas','Dúvidas frequentes','help-circle-outline'],['avaliacoes','Depoimentos/Avaliações','comment-quote-outline'],['galeria','Galeria de fotos','image-multiple-outline'],['agenda','Agenda/Datas','calendar-month-outline'],['localizacao','Localização','map-marker-outline']];
const titles=Object.fromEntries(nav.map(([id,title])=>[id,title]));
const home=computed(()=>!course.value && !titles[page.value] && page.value!=='redes');
const lightbox=ref(null);const dialog=ref();const motion=ref(false);
watch(motion,v=>document.documentElement.classList.toggle('reduce-motion',v));
watch(route,async()=>{document.title=(course.value?.name||titles[page.value]||'Cursos profissionais')+' | '+c.name;await nextTick();document.querySelector('main')?.focus({preventScroll:true})});
const whatsappUrl = message => 'https://wa.me/'+c.whatsapp.replace(/\D/g,'')+'?text='+encodeURIComponent(message);
const priceUrl = item => whatsappUrl(`Olá! Vim pelo site e gostaria de saber os valores e as formas de pagamento do curso ${item.name} — ${item.subtitle}.`);
const floatingUrl = computed(()=>course.value ? priceUrl(course.value) : whatsappUrl('Olá! Vim pelo site da Academia da Manicure e gostaria de informações sobre os cursos.'));
const ebookUrl = computed(()=>whatsappUrl('Olá! Vim pelo site da Academia da Manicure e gostaria de adquirir o e-book. Como faço?'));
const enrollmentUrl = computed(()=>whatsappUrl('Olá! Vim pelo site da Academia da Manicure e gostaria de saber como fazer a matrícula e quais documentos são necessários.'));
async function showPhoto(photo){lightbox.value=photo;await nextTick();dialog.value.showModal()}
</script>
<template>
<a href="#conteudo" class="skip" @click.prevent="focusMain">Pular para conteúdo</a>
<header class="topbar"><a href="#/" class="wordmark">ACADEMIA <span>DA MANICURE</span></a><a href="#/agenda" class="toplink">Agenda de cursos <i class="mdi mdi-arrow-top-right"/></a></header>
<main id="conteudo" tabindex="-1" :class="{home}">
<template v-if="home">
<section class="intro"><img class="brand-logo" src="/media/logo.jpeg" alt="Academia da Manicure — logo com borboleta" width="220" height="212"><p class="eyebrow">APRENDA. PRATIQUE. TRANSFORME.</p><h1>Seu próximo capítulo<br>começa pelas <em>suas mãos.</em></h1><p class="intro-copy">Escolha sua formação e dê o próximo passo<br class="desktop"> com a Academia da Manicure.</p></section>
<section class="courses" aria-label="Escolha seu curso"><a v-for="(item,i) in c.courses" :key="item.id" :href="'#/curso/'+item.id" class="course-card" :class="{'long-title':i===1}"><div class="card-copy"><span class="pill">{{item.tag}}</span><h2>{{item.name}}</h2><p class="course-subtitle">{{item.subtitle}}</p><p class="card-description">{{i===0?'Técnica, acabamento e novas possibilidades para o seu trabalho.':'Uma nova profissão, do cuidado com as mãos à beleza dos pés.'}}</p><span class="card-cta">Conhecer o curso <i class="mdi mdi-arrow-top-right"/></span><span class="card-meta">{{item.hours}} <b>·</b> {{item.days}}</span></div><div class="card-image"><img :src="item.image" :alt="'Imagem do material do curso '+item.name" width="400" height="420"><span class="image-label">{{i===0?'20 EM 1':'PROFISSIONALIZANTE'}}</span></div></a></section>
<section class="ebook-callout" aria-labelledby="ebook-title"><div><p class="eyebrow">CONTINUE APRENDENDO</p><h2 id="ebook-title">E-books da Academia</h2></div><a class="primary" :href="ebookUrl" target="_blank" rel="noopener noreferrer"><i class="mdi mdi-book-open-page-variant-outline" aria-hidden="true"/> Para adquirir o e-book, clique aqui <i class="mdi mdi-arrow-top-right" aria-hidden="true"/></a></section>
</template>
<template v-else>
<a href="#/" class="back"><i class="mdi mdi-arrow-left"/> Voltar ao início</a>
<template v-if="course">
<section class="course-heading"><p class="eyebrow">{{course.tag}}</p><h1>{{course.name}}</h1><h2>{{course.subtitle}}</h2><p>{{course.description}}</p><div class="facts"><span><i class="mdi mdi-clock-outline"/> {{course.hours}}</span><span><i class="mdi mdi-calendar-outline"/> {{course.days}}</span><span><i class="mdi mdi-account-group-outline"/> {{course.capacity}}</span></div><a class="primary" :href="enrollmentUrl" target="_blank" rel="noopener noreferrer">Quero saber sobre a matrícula <i class="mdi mdi-arrow-right"/></a></section>
<img class="course-banner" :src="course.image" :alt="'Imagem do material de '+course.name" width="850" height="300">
<section class="content-block"><p class="eyebrow">SUA FORMAÇÃO</p><h2>O que você vai aprender</h2><p>{{course.audience}}</p><details v-for="(mod,i) in course.modules" :open="i===0"><summary>{{mod.title}}</summary><ol class="curriculum"><li v-for="item in mod.items">{{item}}</li></ol></details></section>
<section class="content-block"><h2>Materiais para as aulas</h2><p>{{course.materialsIntro}}</p><ul class="checklist"><li v-for="m in course.materials">{{m}}</li></ul></section>
<section class="content-block green-block"><h2>{{course.id==='nail-designer'?'Bônus e acompanhamento':'A experiência de aprender'}}</h2><ul class="checklist"><li v-for="b in course.benefits">{{b}}</li></ul></section>

<section class="content-block"><h2>Agenda da turma</h2><div class="dates"><span v-for="d in course.dates">{{d}}</span></div><p>{{course.schedule}}</p><p class="note">Datas publicadas no material do curso. Confirme disponibilidade com a escola.</p><a :href="enrollmentUrl" class="primary" target="_blank" rel="noopener noreferrer">Matrícula e documentos <i class="mdi mdi-arrow-right"/></a></section>
<a :href="course.source" target="_blank" rel="noopener noreferrer" class="source">Consultar apresentação original do curso ↗</a>
<section class="price-contact green-block" v-if="c.whatsapp"><p class="eyebrow">DÊ O PRÓXIMO PASSO</p><h2>Quer saber o valor deste curso?</h2><p>Consulte os preços e as formas de pagamento diretamente com a Academia pelo WhatsApp.</p><a class="primary" :href="priceUrl(course)" target="_blank" rel="noopener noreferrer"><i class="mdi mdi-whatsapp" aria-hidden="true"/> Consultar preço pelo WhatsApp <i class="mdi mdi-arrow-top-right" aria-hidden="true"/></a></section>
</template>
</template>
<template v-if="!course">
<section v-if="home || page==='duvidas'" id="duvidas" class="content-block home-section"><div class="section-heading"><p class="eyebrow">01 / ACADEMIA DA MANICURE</p><h2>Dúvidas frequentes</h2></div><p>Respostas para planejar seu próximo passo.</p><article v-for="[q,a] in c.faq" class="faq-answer"><h3>{{q}}</h3><p>{{a}}</p></article><div class="faq-enrollment"><p>Pronta para dar o próximo passo?</p><a class="primary" :href="enrollmentUrl" target="_blank" rel="noopener noreferrer"><i class="mdi mdi-whatsapp" aria-hidden="true"/> Como fazer a matrícula / Documentos <i class="mdi mdi-arrow-top-right" aria-hidden="true"/></a></div></section>
<section v-if="home || page==='avaliacoes'" id="avaliacoes" class="content-block home-section"><div class="section-heading"><p class="eyebrow">02 / ACADEMIA DA MANICURE</p><h2>Depoimentos/Avaliações</h2></div><template v-if="c.reviews.length"><blockquote v-for="review in c.reviews"><p>“{{review.text}}”</p><cite>{{review.name}}</cite></blockquote></template><div v-else class="empty"><i class="mdi mdi-comment-quote-outline"/><h2>Histórias de quem aprendeu aqui</h2><p>Em breve, depoimentos e avaliações das nossas alunas.</p><a href="#/" class="text-link">Conheça as formações →</a></div></section>
<section v-if="home || page==='galeria'" id="galeria" class="content-block home-section"><div class="section-heading"><p class="eyebrow">03 / ACADEMIA DA MANICURE</p><h2>Galeria de fotos</h2></div><div v-if="c.photos.length" class="gallery"><button v-for="photo in c.photos" @click="showPhoto(photo)"><img :src="photo.src" :alt="photo.alt" loading="lazy"></button></div><div v-else class="empty"><i class="mdi mdi-image-multiple-outline"/><h2>Aprendizado em cada detalhe</h2><p>Em breve, fotos do nosso espaço, das aulas e dos resultados.</p><a href="#/" class="text-link">Explore os cursos →</a></div></section>
<section v-if="home || page==='agenda'" id="agenda" class="content-block home-section"><div class="section-heading"><p class="eyebrow">04 / ACADEMIA DA MANICURE</p><h2>Agenda/Datas</h2></div><p>Encontre as datas da formação que combina com você.</p><article v-for="item in c.courses" class="schedule"><span class="pill">{{item.days}}</span><h2>{{item.name}}</h2><div class="dates"><span v-for="d in item.dates">{{d}}</span></div><p>{{item.schedule}}</p><small>{{item.hours}} · {{item.capacity}} · Disponibilidade a confirmar.</small><a :href="'#/curso/'+item.id" class="text-link">Ver detalhes do curso →</a></article></section>
<section v-if="home || page==='localizacao'" id="localizacao" class="content-block home-section"><div class="section-heading"><p class="eyebrow">05 / ACADEMIA DA MANICURE</p><h2>Localização</h2></div><div class="empty"><i class="mdi mdi-map-marker-outline"/><h2>Seu próximo passo tem um lugar</h2><p>{{c.address||'O endereço e as orientações para chegar serão disponibilizados em breve.'}}</p><a v-if="c.mapUrl" :href="c.mapUrl" class="primary" target="_blank" rel="noopener noreferrer">Como chegar no Google Maps ↗</a></div><iframe v-if="c.mapEmbedUrl" class="location-map" :src="c.mapEmbedUrl" title="Google Maps: Academia da Manicure, Rua San Martin, 165, Rudge Ramos, São Bernardo do Campo" loading="lazy" referrerpolicy="no-referrer-when-downgrade" allowfullscreen></iframe></section>
<section v-if="home || page==='redes'" id="redes" class="content-block home-section"><div class="section-heading"><p class="eyebrow">06 / ACADEMIA DA MANICURE</p><h2>Redes sociais</h2></div><p>Acompanhe as novidades da Academia da Manicure.</p><nav v-if="c.socials.length" class="nav-grid"><a v-for="social in c.socials" :href="social.url" target="_blank" rel="noopener noreferrer"><i :class="'mdi mdi-'+social.icon" aria-hidden="true"/><span>{{social.name}}</span><i class="mdi mdi-arrow-top-right" aria-hidden="true"/></a></nav><div v-else class="empty"><i class="mdi mdi-heart-outline"/><h2>Vamos nos conectar</h2><p>Nossos canais oficiais estarão disponíveis aqui em breve.</p></div></section>
</template>
</main>
<footer><a href="#/">Academia da Manicure</a><p>Aprendizado que abre novas possibilidades.</p><div><a href="#/redes">Redes sociais</a><span>·</span><a href="#/localizacao">Localização</a><span>·</span><a href="/links">Link na bio</a></div><small>© {{new Date().getFullYear()}} Academia da Manicure</small><label class="motion"><input type="checkbox" v-model="motion"> Reduzir animações</label></footer>
<AccessibilityMenu/><a v-if="c.whatsapp" class="whatsapp" :href="floatingUrl" aria-label="Falar com a Academia pelo WhatsApp" target="_blank" rel="noopener noreferrer"><i class="mdi mdi-whatsapp"/></a>
<dialog ref="dialog" @click="e=>{if(e.target===dialog)dialog.close()}"><button @click="dialog.close()" aria-label="Fechar foto">×</button><img v-if="lightbox" :src="lightbox.src" :alt="lightbox.alt"></dialog>
</template>
