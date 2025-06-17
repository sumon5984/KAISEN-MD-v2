import React, { useEffect, useState, useCallback } from "react";
import { useLocation, useNavigate } from "react-router-dom";
import { Helmet } from 'react-helmet';
import { roundNumber, makeid } from "../../fn/fn";
import LoadingSearchAnimation from "../../components/loading_search";
import "./search.css";

import { FaSearch } from "react-icons/fa";
import { IoChevronBackSharp, IoClose } from "react-icons/io5";
import { IoMdCart, IoIosHeart } from "react-icons/io";
import { RiDeleteBin6Line } from "react-icons/ri";
import { BiSolidPurchaseTag } from "react-icons/bi";
import { VscWorkspaceTrusted } from "react-icons/vsc";


export default function Search({ socket }) {
  const navigate = useNavigate();
  const location = useLocation();
  const [For, setFor] = useState(false);
  const [sid, setSid] = useState("");
  const [category, setCategory] = useState([]);
  const [srh, setSrh] = useState({ s: [] });
  const [isan, setIsan] = useState(false);
  const storedUser = localStorage.getItem("AUTH-T");
  const User = storedUser ? JSON.parse(storedUser) : {};
  const getCurrPath = sessionStorage.getItem("currentUrl");
  const [product, setProduct] = useState({
    status: false,
    forSeller: false,
    list: [],
    seller: {}
  });
  const [popup, setPopup] = useState({
    msg: '',
    yes: '',
    args: {}
  });
  const [pageMetadata, setPageMetadata] = useState({
    title: "Product Search - INRL",
    description: "Search and find detailed information about the latest products on INRL.",
    ogTitle: "Product Search - INRL",
    keywords: "inrl, shop, products, kerala, online, website, search",
    ogDescription: "Discover a wide range of products available on INRL with our comprehensive search feature.",
    ogType: "website",
    ogUrl: "https://www.inrl.online/search",
    ogImage: "https://www.inrl.online/icon/logo.png",
    ogSiteName: "INRL",
    twitterTitle: "Product Search - INRL",
    twitterDescription: "Find and explore products easily with our detailed search feature on INRL.",
    twitterImage: "https://www.inrl.online/icon/logo.png",
    canonicalUrl: "https://www.inrl.online/search",
    appleTouchIcon: "https://www.inrl.online/icon/apple-touch-icon.png",
    favicon: "https://www.inrl.online/icon/favicon.ico",
    shortcutIcon: "https://www.inrl.online/icon/favicon.ico",
    maskIcon: "https://www.inrl.online/icon/safari-pinned-tab.svg",
    themeColor: "#ffffff"
  });
 useEffect(() => {
    const main = document.querySelector('.search-content-sr');
    if (!getCurrPath) {
      sessionStorage.setItem('currentUrl', location.pathname);
      sessionStorage.setItem('previousUrl', undefined);
    } else if (location.pathname !== getCurrPath) {
      sessionStorage.setItem('currentUrl', location.pathname);
      sessionStorage.setItem('previousUrl', getCurrPath);
    }

    const searchOnLocal = localStorage.getItem('srh');
    if (searchOnLocal) setSrh(JSON.parse(searchOnLocal));
    const isSharedBy = location.search ? new URLSearchParams(location.search) : false;
    const sharedAddress = isSharedBy ? isSharedBy.get('id') : false;
    const id_product = location.state && location.state.id ? location.state.id : false;
    const search_id = id_product || sharedAddress;

    if (!search_id) {
      navigate('/');
    } else {
      setSid(search_id);
    };
   socket.on("search-results", (v) => {
        if (v.status === true) main.style.display = "block";
        if (v.category) setCategory(v.category);
        setProduct({
          status: v.status,
          message: v.message,
          list: v.list,
          seller: v.seller
        });
        setPageMetadata(a => ({
          ...a,
          title: v.list[0].title,
          ogTitle: v.list[0].title,
          ogImage: v.list[0].img,
          twitterTitle: v.list[0].title,
          twitterImage: v.list[0].img,
          ogUrl: `${a.ogUrl}?id=${sid}`,
          canonicalUrl: `${a.canonicalUrl}?id=${sid}`
        }));
      });
      const handleConnectionSuccess = () => {
        if (location.pathname.includes('/search')) {
          socket.emit('get-searched-output', { auth: User, sid: search_id });
        }
      };
      socket.once('connection-success', handleConnectionSuccess);
      socket.emit('get-searched-output', { auth: User, sid: search_id });
      // Cleanup
      return () => {
        socket.off('connection-success', handleConnectionSuccess);
      };
  }, []);
  
  useEffect(() => {
  if (!product.list) return;

  const lastP = [...product.list];
  const typeofLast = lastP[lastP.length - 1];

  const checkout = () => {
    const element = document.querySelector(`.${styles.pr_list_sr}`);
    if (
      element.scrollTop + element.clientHeight >= element.scrollHeight &&
      typeofLast?.type !== "info"
    ) {
      socket.emit("load-new-product", { 
        auth: User,
        sid: sid,
        forSeller: product.forSeller,
        seller_uuid: For
      });
    }
  };
  const handleProductInfo = (p) => {
    lastP.pop();
    setProduct((a) => ({
      ...a,
      list: [...lastP, ...p],
    }));
  };
  const eventList = document.querySelector(`.${styles.pr_list_sr}`);
  socket.on("new-search-result", handleProductInfo);
  if(eventList) eventList.addEventListener("scroll", checkout);

  return () => {
    socket.off("new-search-result", handleProductInfo);
    if(eventList) eventList.removeEventListener("scroll", checkout);
  };
}, [product, sid, For]);
  const BlurFilter = {
  filter : "blur(1px)"
  };
  const backTo = (to) => {
    if (to === "home:cart") {
      return navigate("/#cart");
    } else {
      const back = sessionStorage.getItem("previousUrl");
      if (back !== undefined) {
        return navigate(back);
      } else {
        navigate("/", {replace: true});
      }
    }
  };
  const search = (e) => {
    const category = document.querySelector(`.${styles.head_sr}`);
    const main = document.querySelector(`.${styles.pr_list_sr}`);
    const sh = document.querySelector(`.${styles.perform_sh_sr}`);
    main.style.filter = "blur(10px)";
    sh.style.display = "block";
    category.style.display = "none";
  };
  const stopSearch = (e) => {
    const category = document.querySelector(`.${styles.head_sr}`);
    const main = document.querySelector(`.${styles.pr_list_sr}`);
    const sh = document.querySelector(`.${styles.perform_sh_sr}`);
    main.style.filter = "none";
    sh.style.display = "none";
    category.style.display = "flex";
  };
  const share = useCallback(() => {
    if (navigator.share) {
        navigator
          .share({
            url: `/search?for=${For}`
          })
          .then(() => {
            console.log("Content shared successfully");
          })
          .catch((error) => {
            console.error("Error sharing content:", error);
          });
      } else {
        alert("Web Share API is not supported in your browser.");
      }
    },[For]);
  const RunASearch = (txt) => {
    if(txt) {
      window.location.href = `/search?id=${txt}`;
    } else {
    const v = document.querySelector(".sh-sr input");
    if (!v.value) return (v.placeholder = "enter something to search..");
    const data = localStorage.getItem("srh");
    const search = data ? JSON.parse(data) : { s: [] };
    search.s.push({ search: v.value, type: "local", id: makeid(8) });
    localStorage.setItem("srh", JSON.stringify(search));
    window.location.href = `/search?id=${v.value}`;
    v.value = "";
    };
  };
  const dltSearch = (id) => {
    const data = localStorage.getItem("srh");
    const search = data ? JSON.parse(data) : { s: [] };
    const res = search.s.filter((a) => a.id !== id);
    localStorage.setItem("srh", JSON.stringify({ s: res }));
    setSrh({ s: res });
  };
  const goCategory = (id) => {
    return navigate(`/category/${id}`);
  };
  const loadProduct = (id) => {
    navigate("/product", { state: { id } });
  };
  const cartUpdate = useCallback((id) => {
  const info = document.querySelector('.info-sr-nt');
  if(!User.reg_pkocd) {
    if(isan) {
      clearTimeout(isan);
      info.style.animation = 'none';
    };
    info.style.display = "block";
    info.innerText = "can't update carts before a successful login";
    info.style.animation = 'notficationPopUp 4s';
    const time = setTimeout(() => {
      info.style.display = "none";
    }, 7200);
    setIsan(time);
  } else {
    const info = document.querySelector('.info-sr-nt');
    const Productss = {...product};
    if(Productss.list.filter(a=>a.id === id)[0].isOnCart === false) {
    const NewList = Productss.list.map(a=> {
        if(a.isOnCart === false && a.id === id) {
           return {...a, isOnCart: true};
        } else return a;
    });
    setProduct(a=>({...a, list: NewList}));
      socket.emit("add-to-cart", { auth: User, id });
      if(isan) {
        clearTimeout(isan);
        info.style.animation = 'none';
      };
      info.style.display = "block";
      info.innerText = "added to cart";
      info.style.animation = 'notficationPopUp 2s';
      const time = setTimeout(() => {
        info.style.display = "none";
      }, 5000);
      setIsan(time);
    } else {
      setPopup({
        msg: 'did your really want to remove this product from cart?',
        yes:'cart',
        args: { id }
      });
      document.querySelector(`.${styles.popup_sr}`).style.display = "block";
      document.querySelector(`.${styles.pr_list_sr}`).style.filter = 'blur(10px)';
    };
  }
}, [sid, product, isan]);
  const runAction = useCallback((v) => {
    document.querySelector(`.${styles.pr_list_sr}`).style.filter = 'none';
    document.querySelector(`.${styles.popup_sr}`).style.display = "none";
    if(v === 'cart') {
        const Productss = {...product};
        const NewList = Productss.list.map(a=> {
        if(a.isOnCart === true && a.id === popup.args.id) {
           return {...a, isOnCart: false};
        } else return a;
        });
        setProduct(a=>({...a, list: NewList}));
        setPopup({msg: '', yes: '',args: {}});
        socket.emit('remove-from-cart', { auth: User, id: popup.args.id });
    };
  }, [product, popup]);
  const closePopup = () => {
    document.querySelector(`.${styles.pr_list_sr}`).style.filter = 'none';
    document.querySelector(`.${styles.popup_sr}`).style.display = "none";
  };
  return (
    <div className={styles.search_res}>
    <Helmet>
      <title>{pageMetadata.title}</title>
      <meta name="description" content={pageMetadata.description} />
      <meta name="keywords" content={pageMetadata.keywords} />
      <meta property="og:title" content={pageMetadata.ogTitle} />
      <meta property="og:description" content={pageMetadata.ogDescription} />
      <meta property="og:type" content={pageMetadata.ogType} />
      <meta property="og:url" content={pageMetadata.ogUrl} />
      <meta property="og:image" content={pageMetadata.ogImage} />
      <meta property="og:site_name" content={pageMetadata.ogSiteName} />
      <meta name="twitter:card" content="summary_large_image" />
      <meta name="twitter:title" content={pageMetadata.twitterTitle} />
      <meta name="twitter:description" content={pageMetadata.twitterDescription} />
      <meta name="twitter:image" content={pageMetadata.twitterImage} />
      <link rel="canonical" href={pageMetadata.canonicalUrl} />
      <link rel="apple-touch-icon" href={pageMetadata.appleTouchIcon} />
      <link rel="icon" href={pageMetadata.favicon} />
      <link rel="shortcut icon" href={pageMetadata.shortcutIcon} />
      <link rel="mask-icon" href={pageMetadata.maskIcon} color={pageMetadata.themeColor} />
      <meta name="theme-color" content={pageMetadata.themeColor} />
  </Helmet>
      {product.status === false ? (
        <LoadingSearchAnimation />
      ) : product.status === "error" ? (
        <h4 className={styles.e_s_e_sr}>Internal server error!</h4>
      ) : null}
      <div className={styles.popup_sr}>
        <div>
           <span onClick={closePopup}>&times;</span>
           <p>{popup.msg}</p>
           <button onClick={()=>runAction(popup.yes)}>Yes</button>
           <button onClick={closePopup}>No</button>
       </div>
      </div>
      <div className={styles.search_content_sr}>
        <div className={styles.info_sr_nt}>loading...</div>
        <div className={styles.perform_sh_sr}>
          <div className={styles.sh_sr}>
            <IoClose className={styles.cls_sr} onClick={stopSearch} />
            <input type="text" placeholder="search something.." />
            <FaSearch className={styles.search_two_sr} onClick={()=>RunASearch()} />
          </div>
          <div className={styles.old_sc_sr}>
            {srh.s.map((a, i) => (
              <div className={styles.sr_list_sh} key={i}>
                <p onClick={()=> RunASearch(a.search)}>{a.search}</p>
                {a.type === "local" ? (
                  <RiDeleteBin6Line
                    className={styles.dlt_my_sh}
                    onClick={() => dltSearch(a.id)}
                  />
                ) : null}
              </div>
            ))}
          </div>
          <div className={styles.category_sh_sr}>
            <h3>Tending categories</h3>
            {category.map((a, key) => (
              <button key={key} onClick={() => goCategory(a.category)}>
                {a.display}
              </button>
            ))}
          </div>
        </div>
        <div className={styles.head_sr}>
          <IoChevronBackSharp className={styles.back_sr_hd} onClick={backTo}/>
          <h3>{sid}</h3>
          <IoMdCart className={styles.sr_sr_hd} onClick={()=>backTo('home:cart')}/>
          <FaSearch className={styles.sr_ca_hd} onClick={search} />
        </div>
        <div className={styles.pr_list_sr}>
          {product.list.map((a, i) => (
            <div key={i}>
              {a.type === "info" ? (
                <div className={styles.pr_sr} id={`prdct-sr${a.id}`}>
                  {a.isAvailable === false ? (
                    <div className={styles.sld_ot_sr}> item sold out</div>
                  ) : null}
                <IoIosHeart
                   className={styles.add_crt_sr}
                   id={`cart-sr${a.id}`}
                   onClick={()=> cartUpdate(a.id)}
                   style={{
                     color: a.isOnCart ?
                       'red' : 'gray'
                   }}
                 />
                  <div id={`prdct-info-sr${a.id}`} style={a.isAvailable === false ? BlurFilter : null }>
                    <img src={a.img} alt={a.title} />
                    <div className={styles.p_info_sr} onClick={() => loadProduct(a.id)}>
                      <h3>{a.title}</h3>
                      <p className={styles.prc_sr}>₹{a.discountPrice} onwards</p>
                      <p className={styles.l_s_sr}>get it low as ₹{a.LastPrice}</p>
                      {a.freeDelivery === "yes" ? (
                        <p className={styles.f_dl_sr}>free delivery</p>
                      ) : (
                        <p className={styles.f_dl_sr}>
                          <span>
                            {Math.ceil(
                              ((a.price - a.LastPrice) / a.price) * 100,
                            )}
                            %
                          </span>{" "}
                          discount
                        </p>
                      )}
                      <div className={styles.tt_sr}>
                        <BiSolidPurchaseTag className={styles.bct_sr} />
                        <p>{roundNumber(a.buys)}</p>
                      </div>
                      {a.isTrusted ? (
                        <div className={styles.tr_sr}>
                          <VscWorkspaceTrusted className={styles.trstd_sr} />
                          <p>inrl</p>
                        </div>
                      ) : null}
                    </div>
                  </div>
                </div>
              ) : (
                <div className={styles.loading_new_sr}>Loading</div>
              )}
            </div>
          ))}
        </div>
      </div>
    </div>
  );
}
