I encountered this same issue In Laravel 11.x and Livewire 3 because I wanted to create classes for separate methods that i will need to include in other classes to solve the same problem.

So, after all efforts what i did was to use Livewire form object.

Form objects allow you to re-use form logic across components and provide a nice way to keep your component class cleaner by grouping all form-related code into a separate class. 

You can check the full Docs for clearer understanding https://livewire.laravel.com/docs/forms#extracting-a-form-object

You can either create a form class by hand or use the convenient artisan command:

    php artisan livewire:form DeleteRecords

this is what the form looks like

    <?php
    
    namespace App\Livewire\Forms;
    
    // use Livewire\Attributes\Validate;
    use Livewire\Form;
    use App\Models\Universities;
    
    class DeleteRecords extends Form
    {
    
        public function DeleteRecord($Model, $id)
        {
            $record = $Model::find($id);
            $record->delete();
        }
    
        public function Swal(){
            return 
                [
                      'title' => 'Great!',
                      'message' => 'Record Deleted successfully',
                      'icon' => 'success'
                ]; 
        }
    }

then the useage like below

    <?php
    
    namespace App\Livewire\Admin;
    
    use Livewire\Component;
    use App\Models\Countries;
    use App\Livewire\Forms\DeleteRecords;
    use Livewire\Attributes\On;
    
    class CountryIndex extends Component
    {
        public DeleteRecords $deletePrompt;
        public function render()
        {
            return view('admin.country-index',
                [
                    'countries' => Countries::latest()->get(),
                ]
            );
        }
    
        protected $listeners = [
            'swal' => '$refresh'
        ];
    
        #[On('Confirm-Delete')]
        public function DeleteRecord($id)
        {
            $this->deletePrompt->DeleteRecord('App\Models\Countries', $id);
    
            $this->dispatch(
                'swal',
                $this->deletePrompt->Swal()
          );
        }
    }


You can see that i can now call and use the form object like so

 

    $this->deletePrompt->DeleteRecord('App\Models\Countries', $id);
