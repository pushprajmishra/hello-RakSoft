# hello-RakSoft
Fsm21
hello friends, I have sent the some files in github.....

sql file....

truncate table po cascade;
ALTER TABLE po_product DROP CONSTRAINT po_product_invoice_fk;
ALTER TABLE po_product DROP column invoice_no;
ALTER TABLE po DROP column invoice_no cascade;
alter table po add column invoice_no text;
alter table po_product add column invoice_no text;
ALTER TABLE po_product ADD CONSTRAINT po_product_invoice_fk FOREIGN KEY (invoice_no) 
REFERENCES po (nvoice_no) ON UPDATE CASCADE ON DELETE CASCADE;
-------------------------------------------------------------------------------------------------------------------------
CREATE OR REPLACE FUNCTION invoice_no_generator()
    RETURNS trigger
    LANGUAGE 'plpgsql'
    COST 100
    VOLATILE NOT LEAKPROOF
AS $BODY$
DECLARE
 chars text[] := '{0,1,2,3,4,5,6,7,8,9,A,B,C,D,E,F,G,H,I,J,K,L,M,N,O,P,Q,R,S,T,U,V,W,X,Y,Z,a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z}';
  result text := '';
  i integer := 0;
BEGIN
    for i in 1..5 loop
       result := result || chars[1+random()*(array_length(chars, 1)-1)];
    end loop;
    result= TO_CHAR(NOW() :: DATE, 'DDMMYYYY') || result;
UPDATE po SET invoice_no=result WHERE id=NEW.id;
RETURN NEW;
END $BODY$;

CREATE TRIGGER invoice_no_generator_po AFTER INSERT ON po FOR EACH ROW
EXECUTE PROCEDURE invoice_no_generator();
-------------------------------------------------------------------------------------------------------------------------
