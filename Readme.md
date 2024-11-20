1.Whenever the Account Phone Field's Updated then all related Contact's Phone fields 
should also get updated with Parents Field's

Public Class AccountTriggerHandler
{
    List<Account> triggernew;
    List<Account> triggerold;
    Map<id,Account> triggernewmap;
    Map<id,Account> triggeroldmap;
    
    Public AccountTriggerHandler()
    {
      triggernew = (List<Account>)trigger.new;
      triggerold = (List<Account>)trigger.old;
      triggernewmap = (Map<Id,Account>) trigger.newmap;
      triggeroldmap = (Map<Id,Account>) trigger.oldmap;
    }
   Public Void doaction()
   {
     Switch on Trigger.OperationType
     {
       When AFTER_UPDATE
       {
         UpdatePhonefield();  
       }
     }
   }
   Public Void UpdatePhonefield()
   {
     List<Contact> UpdateToContact = New List<Contact>();
     Map<id,Account> addmaprecord = New Map<Id,Account>();
     for(Account accrecord : triggernew)
     {
       If(accrecord.Phone != Null && accrecord.Phone != triggeroldmap.get(accrecord.Id).Phone)
       {
         addmaprecord.put(accrecord.Id,accrecord);
       }
     }
     List<Contact> conrecord = [SELECT Id,Phone,AccountId FROM Contact WHERE AccountId IN : addmaprecord.keyset()];
      
     If(conrecord.size()>0)
     {
       for(Contact con : conrecord)
       {
        con.phone = addmaprecord.get(con.AccountId).Phone;
        UpdateToContact.add(con);
       }
     }
    If(! UpdateToContact.isEmpty())
    {
      Update UpdateToContact;
    }
   }
}
=======================================================================================
trigger AccountTrigger on Account (After Update) 
{
   AccountTriggerHandler obj = New AccountTriggerHandler();
   obj.doaction();
}
