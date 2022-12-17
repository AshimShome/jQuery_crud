# jQuery_crud

==>> Cdn

<script src="https://code.jquery.com/jquery-3.6.2.min.js" integrity="sha256-2krYZKh//PcchRtd+H+VyyQoZ/e3EcrkxhM8ycwASPA=" crossorigin="anonymous"></script>

######################### for store Request Start #################################

$(document).ready(function(){
        $('#ShippingAddressForm').on('submit', function(event){
            event.preventDefault();
            $.ajax({
                url     : '{{route("save-shipping-address")}}',
                type    : 'post',
               // dataType: 'html',
                data    : $('#ShippingAddressForm').serialize(),
                success : function(response){
                    console.log(response);
                    jQuery('#staticBackdrop').modal('hide');
                    // location.reload();
                    $('#modelButton').hide();
                },
            })
        });
    });
    
    
    *********************** Controller
public function saveShippingAddress(Request $request)
    {
        // return $resquest->all();
        $validation = $request->validate([
            'contact_person_name' => 'required',
            'address_type' => 'required',
            'address' => 'required',
            'phone' => 'required',
        ]);
        if ($request->ajax()) {
            if (auth('customer')->check() && $validation) {
                // dd($request->save_address,$request->save_billing_address);
                $customerShippingAddress = new ShippingAddress;
                $customerShippingAddress->customer_id = auth('customer')->id();
                $customerShippingAddress->contact_person_name = $request->contact_person_name;
                $customerShippingAddress->address_type = $request->address_type;
                $customerShippingAddress->address = $request->address;
                $customerShippingAddress->city = $request->city; //zilla's
                $customerShippingAddress->zip = $request->zip; //upazilla's
                $customerShippingAddress->phone = $request->phone;
                $customerShippingAddress->latitude = $request->latitude;
                $customerShippingAddress->longitude = $request->longitude;
                // dd($customerShippingAddress);
                $customerShippingAddress->save();
                if($request->save_billing_address == 'checked')
                {
                    $customerBillingAddress = new BillingAddress;
                    $customerBillingAddress->customer_id = auth('customer')->id();
                    $customerBillingAddress->contact_person_name = $request->contact_person_name;
                    $customerBillingAddress->address_type = $request->address_type;
                    $customerBillingAddress->address = $request->address;
                    $customerBillingAddress->city = $request->city; //zilla's
                    $customerBillingAddress->zip = $request->zip; //thana's
                    $customerBillingAddress->phone = $request->phone;
                    $customerBillingAddress->latitude = $request->latitude;
                    $customerBillingAddress->longitude = $request->longitude;
                    $customerBillingAddress->save();
                }
                Toastr::success(translate('Your Address has been Successfully Saved.'));
                return response()->json([
                    'customerAddress' => $customerShippingAddress,
                    'customerBillingAddress' => $customerBillingAddress
                ]);
            }
            else {
                return back()->withErrors(\App\CPU\translate('Login First.'));
            }
        }
    }
    ****************************
    ######################### for store Request end #################################
    
    ######################### for edit Request Start #################################
    
<p>Bill to the same address <a href="#" id="billing_id" data-id = {{$address->id}} class="text-decoration-none text-primary"data-bs-toggle="modal"  data-bs-target="#staticBackdropforBillingAddressEdit" >Edit prosinjit</a></p>

*********************** Controller
public function billing_address_edit(Request $request)
    {
        $id=$request->id;
        $billingAddress = BillingAddress::findOrFail($id);
        return response()->json([
            'billingAddress' => $billingAddress
        ]);
    }
****************************

$(document).on('click','#billing_id',function(e)
        {
            e.preventDefault();
            var billing_id = jQuery(this).data('id');
            $.ajax({
                    url     : "{{ route('billing_address') }}",
                    type    : 'GET',
                    dataType: 'json',
                    data    : {
                        id : billing_id,
                    },
                    success : function(response){
                         console.log(response.billingAddress);
                         let billingAddress=response.billingAddress;
                         $('#b_contact_person_name').val(billingAddress.contact_person_name);
                         $('#b_phone').val(billingAddress.phone);
                         $('#b_address').val(billingAddress.address);
                         $('#b_address_type').val(billingAddress.address_type);
                    },
                });
        });

    
    ######################### for edit Request end #################################
