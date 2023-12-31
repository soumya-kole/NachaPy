# Created based on pyNacha
* Improvements on padding logic
* Error check for Debit/Credit missing entries
* Pluggable Batch increment etc


# NACHA CCD+ ACH file generator module for python. 
## This has been tested with “CCD” batches with addenda records. 
## Forked from carta-ach by Carta, Inc. and modified to include special charater support for Company, Bank and Supplier's Name.


Example:

```
from pyNacha.builder import AchFile

class CustomPluggable(Pluggable):
    @staticmethod
    def file_id_mod():
        return 'A'

settings = {
    'immediate_dest': '123456789', # Your bank's routing number
    'immediate_org': '1098765432', # Bank assigned routing number
    'immediate_dest_name': "BANKNAME",
    'immediate_org_name': 'COMPANYNAMR',
    'company_id': '1098765432',
    'company_name': 'COMPANYNAME',
    'trace_num': '00000000'
}

ach_file = AchFile(settings, CustomPluggable)

entries = [
    {
        'type'           : '22', # type of
        'routing_number' : '12345678',
        'account_number' : '11232132',
        'amount'         : '10.00',
        'id_number': '901398852',
        'name'           : 'Alice&Wanderdust',
        'addenda' : [
            {
                'payment_related_info': 'Here is some additional information',
            }
        ],
    },
    {
        'type'           : '22',
        'routing_number' : '12345678',
        'account_number' : '234234234',
        'amount'         : '150.00',
        'id_number': '901398853',
        'name'           : 'Billy Holiday',
        'addenda' : [
            {
                'payment_related_info': 'Here is some additional information2',
            },
        ],
    },
    {
        'type'           : '22',
        'routing_number' : '12323231',
        'account_number' : '123123123',
        'amount'         : '12.13',
        'id_number': '901398853',
        'name'           : 'Rachel Welch',
    },
]

ach_file.add_batch('CCD', entries, credits=True, debits=True)

print(ach_file.render_to_string())
```

===============================================================================================

### This returns the following NACHA file:

101 123456780 1234567801409020123A094101YOUR BANK              YOUR COMPANY
5200YOUR COMPANY                        1234567890PPDPAYROLL         140903   1123456780000001
62212345678011232132         0000001000               ALICE WANDERDUST        1123456780000001
705HERE IS SOME ADDITIONAL INFORMATION                                             00000000001
627123456780234234234        0000015000               BILLY HOLIDAY           0123456780000002
622123232318123123123        0000001213               RACHEL WELCH            0123456780000003
820000000400370145870000000150000000000022131234567890                         123456780000001
9000001000001000000040037014587000000015000000000002213
9999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999
9999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999999