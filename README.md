 
 #   w a l l e t s - m a n a g e r  
  
 ` v 0 . 5 `   c u r r e n t l y .   E v e r y t h i n g   i s   m o d u l e .   G e t t i n g   r e a d y   f o r   O G L C - B N B   e x c h a n g e   ( v 0 . 5 ) ,   s e t t i n g   u p   N F T   d e v   e n v .  
  
 S t a r t   t h e   s e r v i c e :  
  
 1 .   I n s t a l l   D o c k e r ,   e n a b l e   i t   i n   s y s t e m c t l ,  
 2 .   ` g i t   p u l l `   t h i s   r e p o s i t o r y ,  
 3 .   d o   ` c d   s e r v i c e `   a n d   ` n p m   i n s t a l l ` ,  
 4 .   c r e a t e   . e n v   f i l e   p e e p i n g   i n   e x a m p l e . e n v ,  
 5 .   g o   b a c k   ( ` c d   . . ` )   a n d   s t a r t   w a l l e t s - m a n a g e r   f i n a l l y :  
  
 -   s t a r t u p   w i t h   b l o c k h a i n ' s   ` m a i n n e t ` :   ` d o c k e r - c o m p o s e   u p   - d ` ,  
 -   w i t h   b l o c k h a i n ' s   ` t e s t n e t ` :   ` d o c k e r - c o m p o s e   - f   d o c k e r - c o m p o s e . t e s t n e t . y m l   u p   - - b u i l d   s e r v i c e `  
  
 i f   y o u   g o t   a n   e r r o r      c o n t a c t   m a i n t a i n e r   : )  
  
 # #   A P I  
  
 A P I   m e t h o d s   d o c u m e n t a t i o n .   A c c e s s   a t   ` 1 2 7 . 0 . 0 . 1 : 2 3 1 1 / & `   w i t h o u t   a u t h .   M a i n t a i n e r      @ R i c k C a s t l e 2 0 1 8   ( @ n i k o n o v c c   i n   T e l e g r a m ) .  
  
 B u i l t   w i t h   R E S T   i n   m i n d .   S o   a l l   q u e r y   e x a m p l e s   a r e   J S O N   w h i c h   y o u   h a v e   t o   s e n d   i n   P O S T - d a t a   t o   s e r v i c e .   A n d   t h e r e ' s   e x a m p l e   f o r   e a c h   A P I   m e t h o d :   * Q      Q u e r y   s t r u c t .   R      R e s p o n s e   s t r u c t   ( p r o v i d e d   i f   n e e d e d ,   o t h e r w i s e   j u s t   r e s p o n c e   c o d e ) . *  
  
 I n   e x a m p l e s   y o u   c a n   f i n d   u n u s u a l   t y p e   * W e i * .   I n   b l o c k c h a i n   a l l   m o n e y   a m o u n t s   s h o u l d   b e   p r o v i d e d   i n   W e i .   R e p r e s e n t e d   b y   ` s t r i n g `   i n   J S O N .   L e a r n   m o r e :   h t t p s : / / w w w . i n v e s t o p e d i a . c o m / t e r m s / w / w e i . a s p  
  
 # # #   g a m e - w a l l e t  
  
 g a m e - w a l l e t   ( ` / g a m e - w a l l e t / { m e t h o d } ` )      i t ' s   o u r   ' b a n k ' .   * I n   f u t u r e *   t h e r e   w i l l   b e   a   f e w   t y p e s   ( e x .   ` / g a m e - w a l l e t s / o g l c / & ` )  
  
 # # # #   G E T   / g a m e - w a l l e t  
  
 R e t u r n   g a m e ' s   g a m e - w a l l e t   d a t a :   b l o c k c h a i n   a d d r e s s   a n d   b a l a n c e .  
  
 ` ` ` j s  
 R :  
  
 {  
         a d d r e s s :   s t r i n g ,  
         b a l a n c e :   {  
                 b n b :   w e i ,  
                 o g l c :   w e i  
         }  
 }  
 ` ` `  
  
 # # # #   P O S T   / g a m e - w a l l e t / w i t h d r a w  
  
 W i t h d r a w   m o n e y   f r o m   g a m e - w a l l e t   t o   u s e r - w a l l e t .  
  
 ` ` ` j s  
 Q :  
  
 {        
         t r a n s a c t i o n _ i d :   i n t ,  
         a m o u n t :   w e i ,  
         t o :   i n t ,   / /   i d I n G a m e  
         c u r r e n c y :   s t r i n g   / /   o g l c   o r   b n b  
 }  
 ` ` `  
  
 # # #   u s e r - w a l l e t s  
  
 u s e r - w a l l e t   ( ` / u s e r - w a l l e t s / { u s e r - i d } / { m e t h o d } ` )      i s   a   w a l l e t   w h i c h   e v e r y   u s e r   h a s ,   i t ' s   u s e r ' s   g a m e   a c c o u n t  
  
 ` { u s e r _ i d } `      s h o u l d   b e   t h e   s a m e   a s   u s e r ' s   i d   i n   g a m e ' s   d a t a b a s e .  
  
 # # # #   G E T   / u s e r - w a l l e t s / { u s e r _ i d }  
  
 G e t   u s e r   d a t a :   b a l a n c e   a n d   b l o c k c h a i n   a d d r e s s .  
  
 ` ` ` j s  
 R :  
  
 {        
         b a l a n c e :   {  
                 b n b :   w e i ,  
                 o g l c :   w e i  
         } ,  
         a d d r e s s :   s t r i n g  
 }  
 ` ` `  
  
 # # # #   P U T   / u s e r - w a l l e t s / { u s e r _ i d }  
  
 C r e a t e   n e w   u s e r - w a l l e t   w i t h   t h e   f o l l o w i n g   ` { u s e r _ i d } ` .   R e t u r n s   b l o c k c h a i n   a d d r e s s .  
  
 ` ` ` j s  
 R :  
  
 {        
         a d d r e s s :   s t r i n g  
 }  
 ` ` `  
  
 # # # #   P O S T   / u s e r - w a l l e t s / { u s e r _ i d } / w i t h d r a w  
  
 W i t h d r a w   O G L C / B N B   o u t   o f   g a m e   s y s t e m .   F e e   w i l l   b e   p a i d .  
  
 ` ` ` j s  
 Q :  
  
 {        
         t r a n s a c t i o n _ i d :   i n t ,  
         a m o u n t :   w e i ,  
         t o :   s t r i n g ,   / /   b l o c k c h a i n   a d d r e s s  
         c u r r e n c y :   s t r i n g   / /   b n b   o r   o g l c  
 }  
 ` ` `  
  
 # # #   e x c h a n g e  
  
 # # # #   G E T   / e x c h a n g e  
  
 G e t   e x c h a n g e   i n f o .  
  
 ` ` ` j s  
 R :  
  
 {  
         b n b P r i c e :   i n t ,   / /   h o w   m u c h   O G L C   y o u   c a n   g e t   f o r   1   B N B  
         f e e :   f l o a t   / /   i n t   p e r c e n t a g e   ( e x .   2 5 )  
 }  
 ` ` `  
  
 # # # #   P O S T   / e x c h a n g e / u p d a t e  
  
 C h a n g e   e x c h a n g e   f e e s   o r   b n b P r i c e .   Y o u   c a n   p r o v i d e   o n l y   o n e   v a l u e   ( b n b P r i c e   o f   f e e ) .   N o t i c e :   o n   i n i t   v a l u e   w i l l   b e   f r o m   e n v   i n   d o c k e r - c o m p o s e .  
  
 ` ` ` j s  
 Q :  
  
 {        
         b n b P r i c e :   i n t ,  
         e x c h a n g e F e e :   f l o a t  
 }  
 ` ` `  
  
 # # # #   P O S T   / e x c h a n g e / c a l c u l a t e  
  
 G e t   c a l c u l a t i o n s   f o r   t h e   e x c h a n g e .  
  
 ` ` ` j s  
 Q :  
  
 {        
         c u r r e n c y :   s t r i n g ,  
         a m o u n t :   w e i  
 }  
  
 R :  
  
 {        
         p o s s i b l e :   b o o l ,  
         n e g a t e d F e e :   w e i ,  
         e x c h a n g e :   {  
             b n b :   w e i ,  
             o g l c :   w e i  
         }  
 }  
 ` ` `  
  
 # # # #   P O S T   / e x c h a n g e  
  
 E x c h a n g e .   I n   q u e r y   y o u   s h o u l d   s e n d   ` e x c h a n g e `   f r o m   ` / e x c h n a g e / c a l c u l a t e ` .   G a m e   w i l l   g e t   2   w e b h o o k s   w i t h   t h e   s a m e   ` t r a n s a c t i o n _ i d ` .  
  
 ` ` ` j s  
 Q :   / /   R e s p o n c e   o f   / e x c h a n g e / c a l c u l a t e  
  
 {        
         t r a n s a c t i o n _ i d :   i n t ,  
         b n b :   w e i ,  
         o g l c :   w e i  
 }  
 ` ` `  
  
 # # #   n f t s  
  
  
  
 # # # #   P O S T   / n f t s / b u y  
  
 I t ' s   s i m p l e :   s e n d   m o n e y   f r o m   u s e r - w a l l e t   t o   g a m e - w a l l e t .  
  
 ` ` ` j s  
 Q :  
  
 {        
         t r a n s a c t i o n _ i d :   i n t ,  
         c u r r e n c y :   s t r i n g ,   / /   o g l c   o r   b n b  
         a m o u n t :   w e i ,  
         f r o m :   i n t   / /   i d I n G a m e  
 }  
 ` ` `  
  
 # # #   W e b h o o k s  
  
 w a l l e t s - m a n a g e r   s e n d s   w e b h o o k   t o   g a m e   w h e n   r e q u e s t e d   t r a n s a c t i o n   p r o c e s s e d   o r   u s e r - w a l l e t   w a s   r e f i l l e d .   S o   g a m e   s h o u l d   l i s t e n   P O S T   r e q u e s t s ,   U R L   o f   t h i s   l i s t e n e r   s h o u l d   b e   p a s s e d   i n   . e n v   f i l e .  
  
 * * N o t i c e : * *   i t   w o r k s   o n l y   f o r   g a m e - r e q u e s t e d   t r a n s a c t i o n s   s o   w e b h o o k   w o n ' t   b e   s e n t   w h e n   u s e r   r e f i l l e d   h i s   w a l l e t   f o r   e x .   A n d   t h i s   w i l l   b e   t h e   c a s e   u n t i l   w e   s e t   u p   o u r   b l o c k c h a i n   n o d e s   w i t h   w e b s o c k e t s .  
  
 W e b h o o k   J S O N :  
  
 ` ` ` j s  
 {        
         t r a n s a c t i o n _ i d :   i n t ,  
         t y p e :   s t r i n g ,   / /   s e e   t y p e s   l i s t   b e l o w  
         s u c c e s s f u l :   b o o l ,  
         g a s P a i d :   s t r i n g ,   / /   g a s  
         u s e r :   {   / /   d a t a   a b o u t   r e q u e s t e d   w a l l e t   a f t e r   t r a n s a c t i o n   ( i f   r e q u e s t e d   / u s e r - w a l l e t s / &   t h e r e   w i l l   b e   d a t a   a b o u t   t h e   f o l l o w i n g   u s e r - w a l l e t ,   i f   / g a m e - w a l l e t   -   d a t a   a b o u t   g a m e - w a l l e t )  
                 i d :   i n t ,   / /   w i l l   b e   p r o v i d e d   o n l y   w i t h   t r a n s a c t i o n s   w i t h   u s e r - w a l l e t s  
                 b a l a n c e :   {  
                         b n b :   w e i ,  
                         o g l c :   w e i  
                 } ,  
                 a d d r e s s :   s t r i n g  
         }  
 }  
 ` ` `  
  
 T y p e s   o f   W e b h o o k   e v e n t s :  
  
 1 .   " r e f i l l "      u s e r - w a l l e t   h a s   b e e n   r e f i l l e d   f r o m   ' o u t s i d e '   ( n o   t r a n s a c t i o n _ i d   w i l l   b e   s e n t )  
 2 .   " w i t h d r a w "      t r a n s f e r   f r o m   u s e r - w a l l e t   t o   t h e   e x t e r n a l   a d d r e s s  
 3 .   " p u r c h a s e "      m o n e y   f r o m   * u s e r - w a l l e t *   t o   * g a m e - w a l l e t *   t r a n s a c t i o n  
 4 .   " e x i t "      m o n e y   f r o m   * g a m e - w a l l e t *   t o   * u s e r - w a l l e t *   t r a n s a c t i o n .   T h i s   t y p e   i s   a l m o s t   s i m i l a r   t o   " r e f i l l " ,   b u t   i t   h a s   t r a n s a c t i o n _ i d ,   b u t   " r e f i l l "   d o n ' t   ( b e c a u s e   i t   c a n ' t   b e   r e q u e s t e d   b y   t h e   g a m e )  
 5 .   " r e c l a i m "   - -   u s e r   s e n t   t o   u s e r - w a l l e t   h i s   N F T   b o u g h t   f r o m   m a r k e t   a n d   s e r v i c e   h a s   e x c h a n g e d   i t   t o   O g l e  
  
 I n   f u t u r e   t h e r e   w i l l   b e   a l s o   N F T - e v e n t s .  
  
 # # #   B a c k u p s  
  
 Y o u   k n o w ,   w i t h o u t   b a c k u p s   y o u   c a n   l o o s e   e v e r y t h i n g .   S o ,   s e e   ` / d b / b a c k u p . s h `   a n d   ` / d b / r e s t o r e . s h ` .   ` b a c k u p . s h `   s h o u l d   b e   s e t   i n   C R O N .  
 